---
layout: post
title: Taking the effort out of server configuration using Ansible
comments: true
tags:
  - ansible
  - linux
  - systems adminstration
---

*This article was originally published in the [NorDevCon](http://www.nordevcon.com/) 2016 conference programme.*

Ansible is an IT automation tool that is growing in popularity. It is ideally
suited for configuration management, i.e. automating the configuration of your
development and production infrastructure. 

Ansible is a relatively new addition to the "DevOps" arena (first released in
2012) and it has quite a different philosophy to some of the more well
established players in the field. Most notably, it is "agent-less"; i.e. there
is no need to have an "agent" pulling updates from a "master" configuration
manager.

Ansible has been designed to be easy to use and it achieves this through
two aspects of its architecture:

- It uses a push based method to interact with the hosts (the machines to be configured)
- It uses OpenSSH as its authentication method

What this means in practise is that you can install Ansible on your laptop and
as long as you have setup password-less ``ssh`` to the machines you are wanting
to interact with you are ready to go. In other words no master, no databases,
no services; no fighting the system that is meant to be making your life
easier!


## Listing your inventory

Ansible has the concept of an inventory where you list all of the hosts that
you want to be able to interact with through Ansible. The inventory is a plain
text file using an INI-like format. The default path to the inventory file is
``/etc/ansible/hosts``. However, you can provide an alternative path as a
command line argument using the ``-i`` option.

Below is an example ``hosts`` file that groups three web servers into a
``webservers`` group.

```
[webservers]
web1.example.com
web2.example.com
web3.example.com
```

It is also possible to create aliases and specify host specific variables.
Below is an example of an alias to enable Ansible to communicate with a Vagrant
generated virtual machine.

```
testserver ansible_ssh_host=127.0.0.1 ansible_ssh_port=2222 ansible_ssh_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key ansible_sudo=yes
```


## Interacting with Ansible

There are two different ways of interacting with ansible: ad-hoc commands and playbooks.
Ad-hoc commands are useful for quick tasks that you don't want to save for later. Whereas
playbooks provides a means to specify reproducible configuration recipes.

Ad-hoc commands are accessed using the ``ansible`` program and can be useful if
you want to do something quickly. For example, suppose that there was a
critical security patch for bash that you needed to apply to all your
webservers rapidly.

```
ansible webservers -m apt -a "name=bash state=latest update_cache=yes"
```

There are a few things to note in the command above. The first argument
(``webservers``) is the host group defined in the inventory. The ``-m apt``
option states that we want to use Ansible's ``apt`` module, in other words our
servers are running on a Debian based OS such as Ubuntu. Finally, the ``-a ...``
option specifies the arguments to supply, i.e. we want to install the latest
version of ``bash``.

It is worth noting that Ansible does not try to abstract away the layer of
package management. As such there are separate modules for ``apt``, ``yum``,
``homebrew``, etc. This is useful in that the functionality of Ansible modules
are not limited by the constraint of a set of common features. For example in
this case we make use of the ``update_cache`` option to run the equivalent of
``apt-get update`` before the we try to install the latest version of bash.


## Batteries included as Ansible modules

Ansible comes with a large number of built-in modules for configuring your
systems. These include modules for package management, performing systems
administration tasks, working with files, interacting with source control
programs, and much more.

## Reproducible configuration scripts using playbooks

Ansible playbooks allow you to create reproducible recipes for configuring your
servers. Playbooks are written in YAML and are meant to be human readable. As
YAML is simply a data serialisation language, a playbook can be thought of as
"infrastructure as data".

Below is a basic playbook for configuring a firewall using ``firewalld``. In
real life a playbook would be written to configuration the entire system.

```yaml
---

- name: configure a web server firewall using firewalld
  hosts: testserver
  
  tasks:
    - name: install firewalld 
      apt: name=firewalld state=present

    - name: ensure that firewalld is started and enable at boot
      service: name=firewalld enabled=yes state=started

    - name: open up port 80 for tcp
      firewalld: port=80/tcp permanent=yes state=enabled
      notify: restart firewalld

  handlers:
    - name: restart firewalld
      service: name=firewalld state=restarted
```

There are a few things to note in the playbook above. The host(s) that the
playbook is defined to be applied to are defined in the ``hosts`` entry. In
this case it is only run against the Vagrant ``testserver`` alias we defined in
the inventory earlier.  The ``apt`` module is used to install ``firewalld`` and
the ``service`` module is used to ensure that ``firewalld`` is started and
enabled at boot. Finally, we use Ansible's ``firewalld`` module to open up port
80.  Note that this task makes use of the ``notify`` action to trigger the
``restart firewalld`` handler, which we define at the end of the playbook.

## What now?

There is much more to Ansible than what has been outlined here. The key is to
build things up bit by bit. You don't need to use every feature of Ansible to
get a job done.

For more information about Ansible have a look at the
[Ansible documentaiton](http://docs.ansible.com/).
If you liked this article you may also be interested in the other Ansible
tutorials on this site, which illustrate the use of Ansible as a tool to install
scientific software.

- [How to create automated and reproducible work flows for installing scientific software]({% post_url 2015-04-02-how-to-create-automated-and-reproducible-work-flows-for-installing-scientific-software %})
- [How to create reusable Ansible components]({% post_url 2015-04-11-how-to-create-reusable-ansible-components %})
- [How to manage firewalls using ferm and Ansible]({% post_url 2015-04-24-how-to-manage-firewalls-using-ferm-and-ansible %})
- [Ansible playbook for installing the Gbrowse genome browser]({% post_url 2015-04-18-ansible-playbook-for-installing-the-gbrowse-genome-browser %})
