---
layout: post
title: Ansible playbook for installing the GBrowse genome browser
comments: true
tags:
  - ansible
  - linux
  - systems adminstration
  - bioinformatics
---

![GBrowse screenshot](/images/gbrowse-screenshot.png)

In previous posts I have described [how to use ansible to create automated and
reproducible work flows for installing scientific
software]({% post_url 2015-04-02-how-to-create-automated-and-reproducible-work-flows-for-installing-scientific-software %})
and [how to create reusable Ansible
components]({% post_url 2015-04-11-how-to-create-reusable-ansible-components %}).
In this post we will create a playbook for installing the genome browser
[GBrowse](http://gbrowse.org/index.html) and in the process we will learn how
to install and manage services, such as Apache, using Ansible.

## Adding ``Bio::Graphics`` to the ``bio_perl`` role

GBrowse does not only depend on ``Bio::Perl`` it also depends on
``Bio::Graphics``. At this point we could add a role for installing
``Bio::Graphics``. However, I prefer to add the installation of it to the
existing ``bio_perl`` role.

It turns out that ``Bio::Graphics`` depends on ``GD``, which I struggle to
install using ``cpanm``. However, it is available in a pre-compiled form from
the CentOS repositories so we can install ``perl-GD`` from there using ``yum``.

Furthermore, it turned out that the ``Bio::Graphics`` had an implicit
dependency on the ``CGI`` module.

Please update the ``roles/bio_perl/tasks/main.yml`` file to look like the below.

```yaml
---
# Install and configure Bio::Perl and Bio::Graphics.

# Bio::Graphics requires GD.
# However, I cannot work out how to install GD using cpanm,
# so installing it using yum instead.
- name: install perl-GD
  yum: name=perl-GD
       state=present

- name: install implicit Bio::Perl dependencies
  cpanm: name={{ "{{ item " }}}}
  with_items:
    - Time::HiRes
    - LWP::UserAgent

- name: install implicit Bio::Graphics dependencies
  cpanm: name=CGI

- name: install BioPerl
  cpanm: name={{ "{{ item " }}}}
  with_items:
    - Bio::Perl
    - Bio::Graphics
```

## Installing and configuring Apache

As the name implies GBrowse is a web based tool so to serve it we need to
install Apache. Let us create create a new role for this.  Copy and paste the
text below into the file ``roles/apache/tasks/main.yml``.

```yaml
---
# Install and configure Apache.

- name: install apache
  yum: name=httpd
       state=present

- name: start apache and enable at boot
  service: name=httpd
           enabled=yes
           state=started
```

The code above introduces us to the Ansible [service
module](http://docs.ansible.com/service_module.html). The service module is
used to interact with services managed by ``initd`` (or ``systemd`` on CentOS
7). In the ``service`` task above we ask for the service to be started and for
it to be enabled at boot.

Now suppose that we wanted to restart Apache at some point in our Ansible
script. For example after having installed another piece of software that was
served by Apache, such as GBrowse. This can be achived using Ansible's concept
of handlers. Let us therefore add a handler for restarting apache. Copy and
paste the code below into the file ``roles/apache/handlers/main.yml``.

```yaml
---

- name: restart apache
  service: name=httpd
           state=restarted
```

Now any task in a playbook that makes use of the ``apache`` role can restart
Apache by adding the directive ``notify: restart apache``. We will see an
example of this later on in the post towards the end of the ``gbrowse`` role.


## Creating the ``gbrowse`` role

We are now in a position to create the ``gbrowse`` role for configuring and
installing the GBrowse software. Let us start by defining the Ansible roles it
depends on. Copy and paste the code below into the file
``roles/gbrowse/meta/main.yml``.

```yaml
---
dependencies:
  - { role: apache }
  - { role: bio_perl }
```

GBrowse has got pretty good [installation
notes](http://search.cpan.org/src/LDS/GBrowse-2.54/README) and following them
we only need to deal with a couple issues: a couple of undocumented Perl module
dependencies and the fact that the resulting ``Build`` script requires
interactive answers. The former is easy to deal with, we simply install the
missing Perl modules using ``cpanm``. However, the latter is more tricky.

Ansible is not really meant to deal with interactive tasks. This means that
installers that ask a lot of questions pose a problem. However fortunately in
this case the ``./Build config`` command provides sensible defaults that we can
accept and we can simply answer no to all the questions posed by ``./Build
install``. This means that we can use a work around outlined in a [post by Craig
Marvelley](http://marvelley.com/blog/2014/04/23/handling-interactive-ansible-tasks/).

Copy and paste the code below into the file ``roles/gbrowse/tasks/main.yml``.

```yaml
---
# Install and configure the gbrowse genome browser.

- name: install undocumented dependencies
  cpanm: name={{ "{{ item " }}}}
  with_items:
    - Date::Parse
    - Term::ReadKey

- name: install remaining perl module dependencies
  cpanm: name={{ "{{ item " }}}}
  with_items:
    - CGI::Session
    - Digest::MD5
    - File::Temp
    - IO::String
    - JSON
    - Storable
    - Statistics::Descriptive
    - DBI
    - Net::SMTP
    - DBD::SQLite

- name: download the gbrowse tarball
  get_url: url=http://search.cpan.org/CPAN/authors/id/L/LD/LDS/GBrowse-2.54.tar.gz
           dest=/tmp/

- name: unpack the gbrowse tarball
  command: tar -zxf GBrowse-2.54.tar.gz
  args:
    chdir: /tmp/
    creates: /tmp/GBrowse-2.54/LICENSE

- name: build the installer
  command: perl Build.PL
  args:
    chdir: /tmp/GBrowse-2.54/
    creates: /tmp/GBrowse-2.54/Build

# For more detail on ``yes ' ' |`` syntax for accepting default values see:
# http://marvelley.com/blog/2014/04/23/handling-interactive-ansible-tasks/
- name: configure the install accepting all default values
  shell: yes '' | ./Build config
  args:
    chdir: /tmp/GBrowse-2.54/

- name: install gbrowse answering no to all interactive questions
  shell: yes 'n' | ./Build install
  args:
    chdir: /tmp/GBrowse-2.54/
    creates: /etc/httpd/conf.d/gbrowse2.conf
  notify: restart apache
```

Note the ``notify: restart apache`` directive added to the final task above.
This will ensure that Apache is restarted after GBrowse has been installed.

One of the questions we answer "no" to in the interactive installer is to
register our use of GBrowse. If you find this tool useful the developers of it
would appreciate if you registered. You can do this at any point by running the
command ``./Build register``.

## Creating the playbook

Now create a playbook named ``gbrowse.yml`` at the same level as your
``roles`` directory with the code below.

```yaml
---
- hosts: all
  sudo: True

  roles:
    - gbrowse
```

I am using the same Vagrant setup as outlined in the post on [how to create
automated and reproducible work flows for installing scientific
software]({% post_url 2015-04-02-how-to-create-automated-and-reproducible-work-flows-for-installing-scientific-software %}).
So to run the playbook I simply use the command:

```
$ ansible-playbook -i hosts playbook.yml
```

When the playbook finished running I could view the GBrowse application
in my browser by going to the url ``http://192.168.33.10/gbrowse2/``
(``192.168.33.10`` being the private network specified in the Vagrant file from
the previous post).


## Conclusion

In this post I have shown you how to create a reproducible and automated work
flow for installing the GBrowse genome brower using Ansible.

We created a role for installing and managing Apache. This introduced us to
Ansible's ``service`` module and the concept of "handlers" that can be
"notified" by other tasks in a playbook.

In the
[next post]({% post_url 2015-04-24-how-to-manage-firewalls-using-ferm-and-ansible %})
we will look into how we can manage the firewall of our
machine using Ansible and ferm.
