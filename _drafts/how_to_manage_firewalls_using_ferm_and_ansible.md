---
layout: post
title: How to manage firewalls using ferm and Ansible
comments: true
tags:
  - ferm
  - ansible
  - linux
  - systems adminstration
---

In the
[previous post]({% post_url 2015-04-18-ansible-playbook-for-installing-the-gbrowse-genome-browser%})
we created an Ansible playbook for installing the
GBrowse genome browser. As the name implies GBrowse is a browser based
application and it serves web pages over http using Apache. If one is
installing this software as a service to be made more widely accessible one
needs to start thinking about security. In this post we will therefore
configure a firewall for our machine.

## iptables

The standard tool for setting up firewalls on Linux is ``iptables``. It is a
way to set up policy chains to allow or block traffic to, from and through the
machine of interest. If you have not come across or managed ``iptables`` before
I recommend that you have a look at howtogeek's  [Beginner's Guide to iptables,
the Linux
Firewall](http://www.howtogeek.com/177621/the-beginners-guide-to-iptables-the-linux-firewall/)
and Major Hayden's [Best practices:
iptables](https://major.io/2010/04/12/best-practices-iptables/).

## ferm

However, managing firewalls using ``iptables`` can be a pain. Several tools have
therefore evolved to make things easier. In this post we will be using a
program called [ferm](http://ferm.foo-projects.org) (for Easy Rule Making).

When configuring a firewall it is easy to lock oneself out of the machine one
is configuring. The most common scenario for this is setting the default policy
to drop incoming connections and then accidentally flushing the connection
rules, including the rule to accept ``ssh`` connections, leaving the server
inaccessible. To avoid this scenario we will configure the default policy to
accept incoming connections and to secure the server we will include a rule to
drop any incoming connections that do not match any other rules.

Below is a list stating the behaviour that we want from the ``INPUT`` chain of
our firewall.

- We want the default policy to accept incoming connections
- We want to enable connection tracking
- We want to be able to ``ping`` the machine
- We want to be able to ``ssh`` into the machine
- We want to be able to add custom rules using Ansible
- Finally, we want to drop any incoming connections that do not match any rules

The behaviour that we want from the ``OUTPUT`` and ``FORWARD`` chains are
simpler.  We do not want to limit any outgoing connections so we will set the
output policy to accept all connections and because we are not configuring a
router we will set the policy of the forward chain to drop all connections.

We can configure the behaviour above using the ``ferm.conf`` file below.

```
# Ferm script for configuring iptables.

table filter {

    chain INPUT {
	# Set the default policy to ACCEPT to avoid getting
	# accidentally locked out.
        policy ACCEPT;

        # Connection tracking.
        mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;

        # Allow local connections.
        interface lo ACCEPT;

        # Respond to ping.
        proto icmp icmp-type echo-request ACCEPT;

        # Allow ssh connections.
        proto tcp dport ssh ACCEPT;

        # Ansible specified rules.
        
        # Because the default policy is to ACCEPT we DROP
        # everything that comes through to this stage.
        DROP;
    }

    # Outgoing connections are not limited.
    chain OUTPUT policy ACCEPT;

    # This is not a router.
    chain FORWARD policy DROP;
}
```

If you have ``ferm`` installed you can apply the firewall above using the command below.

```
$ sudo ferm ferm.conf
```

Note that in the ``ferm.conf`` file above we have an empty section marked by the comment
``# Ansible specified rules.``. We will use this to dynamically alter the
firewall rules during the running of our Ansible playbook.

## Integrating ferm with Ansible

Let us create an Ansible role for installing and configuring ``ferm``. Copy
and paste the code below into a file named ``roles/ferm/task/main.yml``.

```yaml
---
# Install and configure ferm.
#

# The ferm program is in the epel repository so we need
# to enable it. This could be a separate role, but this
# is left as an exercise for the reader.
- name: enable the epel repo
  yum: name=epel-release
       state=present

# We need to install libselinux-python on the target
# machine to be able to use Ansible to copy the ferm.conf
# file to the /etc/ferm/ directory. It would be reasonable
# to move this task into a separate role for installing common
# software, again this is left as an exercise for the reader.
- name: install libselinux-python
  yum: name=libselinux-python
       state=present

- name: install ferm
  yum: name=ferm
       state=present

- name: add /etc/ferm directory
  file: path=/etc/ferm
        mode=0700
        state=directory

- name: add the ferm.conf file to /etc/ferm
  copy: src=ferm.conf
        dest=/etc/ferm/ferm.conf
  notify: run ferm
```

Note that the last task copies the ``ferm.conf`` file we created above to the
target machine. However, for this to work Ansible expects the ``ferm.conf``
file to be located in the directory named ``roles/ferm/files/``. Let us
therefore create this directory and move the file there.

```
$ mkdir roles/ferm/files
$ mv ferm.conf roles/ferm/files/
```

In the
[previous post]({% post_url 2015-04-18-ansible-playbook-for-installing-the-gbrowse-genome-browser%})
I introduced the concept of handlers that could be
notified by other tasks. Let us create a handler for applying the ``ferm``
rules. Copy and paste the code below into a file named
``roles/ferm/handlers/main.yml``.

```yaml
---

- name: run ferm
  command: ferm /etc/ferm/ferm.conf
  notify: save iptables

- name: save iptables
  command: service iptables save
```

Now we have a handler named ``run ferm``, which when notified will run the
command ``ferm /etc/ferm/ferm.conf`` and in turn notify the ``save iptables``
handler, which makes sure that the firewall rules persist if the machine is
rebooted.

Let us add this role to our playbook. Update the ``gbrowse.yml`` file so that
it looks like the below (we have only added the ``ferm`` role).

```yaml
---
- hosts: all
  sudo: True

  roles:
    - ferm
    - gbrowse
```

However, if you run the ``gbrowse.yml`` playbook at this point the GBrowse
application will stop working as port 80 will be closed. Let us therefore add a
task to open up ports 80 (http) and 443 (https) to the ``apache`` role. Edit
the file ``roles/apache/tasks/main.yml`` to look like the below.

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

- name: open up the http and https ports
  lineinfile: dest=/etc/ferm/ferm.conf
              line='proto tcp dport (http https) ACCEPT;'
              insertafter='# Ansible specified rules.'
  notify: run ferm
```

In the above we make use of Ansible's [lineinfile
module](http://docs.ansible.com/lineinfile_module.html) to insert a new rule to
the ``ferm.conf`` file.

## Results

Let us run the playbook and find out what the resulting ``iptables`` firewall
looks like. Here I am using the same Vagrant/Ansible setup as described in [how
to create automated and reproducible work flows for installing scientific
software]({% post_url 2015-04-02-how-to-create-automated-and-reproducible-work-flows-for-installing-scientific-software %}).

```
$ ansible-playbook -i hosts gbrowse.yml
...
$ vagrant ssh
Last login: Thu Apr 16 02:03:00 2015 from 192.168.33.1
[vagrant@localhost ~]$ sudo iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
DROP       all  --  0.0.0.0/0            0.0.0.0/0           state INVALID 
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           icmp type 8 
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:22 
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:443 
DROP       all  --  0.0.0.0/0            0.0.0.0/0           

Chain FORWARD (policy DROP)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

## Discussion

If you have public facing machines you need to think about security. However
managing firewalls using ``iptables`` directly can be a pain.

In this post I have outlined how you can integrate ``ferm`` and Ansible to
manage your firewall. The cool thing about this approach is that the role of
interest, in this case ``apache``, is responsible for opening up the relevant
ports.

Furthermore as the ``/etc/ferm/ferm.conf`` file will be re-written every time
you run the playook your rules will be updated both if you add or remove roles
from the playbook. In other words if you removed the ``apache`` role and ran
the playbook ports 80 and 433 would be closed at the end when the handlers were
executed (handlers notified during a playbook are executed at the end of it).

Finally, note that security is a complex topic and that the reading of this
post should not be taken as a substitute for a proper understanding of how to
manage firewalls. That is a roundabout way of stating that I do not take
responsibility for any security breaches that you encounter.
