---
layout: post
title: How to create reusable Ansible components
comments: true
tags:
  - ansible
  - linux
  - systems adminstration
---

![Tools pop art.](/images/reusable-components-pop-art.jpg)

In the [previous post]({% post_url 2015-04-02-how-to-create-automated-and-reproducible-work-flows-for-installing-scientific-software %})
I described how to create reproducible and automated work flows for installing
scientific software using [Ansible](http://www.ansible.com/home). In the end we
had an Ansible playbook for installing ``Bio::Perl``. The playbook did
many things. It installed ``gcc`` and ``cpanm`` as well as ``Bio::Perl``. In
this post I will show how we can split these tasks out into reusable components
using Ansible's concept of "roles".

Let us have a look at the Ansible playbook from the end of the previous post.

```yaml
---
- hosts: all
  sudo: True

  tasks:
    - name: install gcc required to build some Perl modules
      yum: name=gcc
           state=present

    - name: install cpan and perl-devel
      yum: name={{ "{{ item " }}}}
           state=present
      with_items:
        - perl-devel
        - perl-CPAN

    - name: download cpanm
      get_url: url=https://cpanmin.us/
               dest=/tmp/cpanm.pl
               mode=755

    - name: install cpanm so that we can use the ansible cpanm module
      command: perl cpanm.pl App::cpanminus
      args:
        chdir: /tmp/
        creates: /usr/local/bin/cpanm

    - name: add cpanm symbolic link to /usr/bin/
      file: src=/usr/local/bin/cpanm
            dest=/usr/bin/cpanm
            state=link

    - name: install implicit Bio::Perl dependencies
      cpanm: name={{ "{{ item " }}}}
      with_items:
        - Time::HiRes
        - LWP::UserAgent

    - name: install BioPerl
      cpanm: name=Bio::Perl
```

Looking at the above there are at least three reusable roles: ``build_tools``
(for installing ``gcc``; this role could grow to include more build tools in the
future), ``cpanm`` (for installing and configuring ``cpanm``), and ``bio_perl``
(for installing ``Bio::Perl`` and its implicit dependencies). I guess one could
argue that the implicit dependencies of ``Bio::Perl`` could be split out into
individual roles, but for now I think that would be too granular.

To create Ansible roles we need a directory named ``roles``. Let us create it
along with the directories required for the ``build_tools`` role.

```
$ mkdir -p roles/build_tools/tasks
```

Now we move the task of installing ``gcc`` into the ``build_tools`` role by
copying and pasting the text below into the file
``roles/build_tools/tasks/main.yml``.

```yaml
---
# Install and configure build tools.

- name: install gcc
  yum: name=gcc
       state=present
```

We now need to remove the ``gcc`` task from the playbook and add the
``build_tools`` role. Modify the ``playbook.yml`` file to look like the
below.

```yaml
---
- hosts: all
  sudo: True

  roles:
    - build_tools

  tasks:

    - name: install cpan and perl-devel
      yum: name={{ "{{ item " }}}}
           state=present
      with_items:
        - perl-devel
        - perl-CPAN

    - name: download cpanm
      get_url: url=https://cpanmin.us/
               dest=/tmp/cpanm.pl
               mode=755

    - name: install cpanm so that we can use the ansible cpanm module
      command: perl cpanm.pl App::cpanminus
      args:
        chdir: /tmp/
        creates: /usr/local/bin/cpanm

    - name: add cpanm symbolic link to /usr/bin/
      file: src=/usr/local/bin/cpanm
            dest=/usr/bin/cpanm
            state=link

    - name: install implicit Bio::Perl dependencies
      cpanm: name={{ "{{ item " }}}}
      with_items:
        - Time::HiRes
        - LWP::UserAgent

    - name: install BioPerl
      cpanm: name=Bio::Perl
```

In the above it is worth noting that one can mix ``roles`` and ``tasks`` in the
same playbook. This is useful when one wants to create a playbook that makes
use of some reusable roles but which also needs to perform some non-reusable
tasks.

Now we can try running the playbook to make sure that we have not broken
anything. Note that the output now reflects the fact that the ``install gcc``
task is being called from within the ``build_tools`` role.

```
$ ansible-playbook -i hosts playbook.yml

PLAY [all] ********************************************************************

GATHERING FACTS ***************************************************************
ok: [scicomp.example.com]

TASK: [build_tools | install gcc] *********************************************
changed: [scicomp.example.com]

...
```

Let us now create directory structures for the ``cpanm`` and ``bio_perl`` roles.

```
$ mkdir -p roles/{cpanm,bio_perl}/tasks
```

For the ``cpanm`` role cut and paste the code below into the file
``roles/cpanm/tasks/main.yml``.

```yaml
---
# Install and configure cpanm.

- name: install cpan and perl-devel
  yum: name={{ "{{ item " }}}}
       state=present
  with_items:
    - perl-devel
    - perl-CPAN

- name: download cpanm
  get_url: url=https://cpanmin.us/
           dest=/tmp/cpanm.pl
           mode=755

- name: install cpanm so that we can use the ansible cpanm module
  command: perl cpanm.pl App::cpanminus
  args:
    chdir: /tmp/
    creates: /usr/local/bin/cpanm

- name: add cpanm symbolic link to /usr/bin/
  file: src=/usr/local/bin/cpanm
        dest=/usr/bin/cpanm
        state=link
```

And the code below into the file ``roles/bio_perl/tasks/main.yml``.

```yaml
---
# Install and configure Bio::Perl.

- name: install implicit Bio::Perl dependencies
  cpanm: name={{ "{{ item " }}}}
  with_items:
    - Time::HiRes
    - LWP::UserAgent

- name: install BioPerl
  cpanm: name=Bio::Perl
```

Finally let us update the ``playbook.yml`` file so that it looks like the below.

```yaml
---
- hosts: all
  sudo: True

  roles:
    - build_tools
    - cpanm
    - bio_perl
```

That is much cleaner! Furthermore the roles can be reused in other
playbooks as and when we need them.

## Adding dependencies

It might be obvious to us now that the ``bio_perl`` role depends on the
``build_tools`` and ``cpanm`` roles. However, it may be less obvious as the
playbook grows or when we want to create a new playbook that makes use of the
``bio_perl`` module.

It is possible to make dependencies explicit when using Ansible roles. To
do this we will need to add a ``meta`` directory to our ``bio_perl`` role.

```
$ mkdir roles/bio_perl/meta
```

Now copy and paste the code below into the file ``roles/bio_perl/meta/main.yml``.

```yaml
---
dependencies:
  - { role: build_tools}
  - { role: cpanm }
```

At this point one can reduce the ``playbook.yml`` file to include only the
``bio_perl`` module as the ``build_tools`` and ``cpanm`` modules will be
pulled in as dependencies.

```yaml
---
- hosts: all
  sudo: True

  roles:
    - bio_perl
```

The content of the playbook now really reflects the original intent: to install
``Bio::Perl``.

## Summary

Ansible has the concept of "roles" that can be used to create reusable
components. To create a role one simply needs to adhere to Ansible's
conventions of naming files and structuring directories.  In its most basic
form a role takes the form of tasks within a file named
``roles/name_of_role/tasks/main.yml``.

In this post we also used the file ``role/bio_perl/meta/main.yml`` to specify
the dependencies of the role. This meant that the content of the final playbook
was succinct and reflected the intent for which it was created, namely to install
``Bio::Perl``. Furthermore, by explicitly stating the dependencies of the
``bio_perl`` role we made it easier to reuse.

Finally, we also noted that it is possible to pick and mix roles and tasks
within a single playbook. This can be useful when creating playbooks that have
both reusable and non-reusable components within them.

## Further reading

The functionality of Ansible roles are not limited to what I have described in
this post. For more information on what they can do have a look at the [Ansible
documentation](https://docs.ansible.com/playbooks_roles.html).

In the
[next post]({% post_url 2015-04-18-ansible-playbook-for-installing-the-gbrowse-genome-browser %})
we will create a playbook for installing the GBrowse genome browser and learn
how to manage services, such as Apache, using Ansible.
