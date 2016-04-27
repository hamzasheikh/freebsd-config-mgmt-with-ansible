Introduction to FreeBSD Configuration Management with Ansible
=============================================================

Introduction
------------

This repo is a quick start way to begin configuration management with Ansible
on a fresh install of FreeBSD. I'm assuming these things:

* Ansible is installed on your local machine.
* On the remote FreeBSD machine:

  * *root* user has a password set.
  * A user called *freebsd* exists.
  * Network and remote access through SSH is working.

SSH Config
----------

Configure SSH client config on your local machine. Add this to
*~/.ssh/config*. Modify as needed.

::

    Host rpi2
        HostName rpi2
        Port 22
        User freebsd
        AddressFamily inet
        CheckHostIP no
        StrictHostKeyChecking no
        UserKnownHostsFile /dev/null

Since I rebuild my local FreeBSD box many times I use **highly insecure** SSH
settings. You may want to use more secure settings for *CheckHostIP*,
*StrictHostKeyChecking*, and *UserKnownHostsFile*, especially when using
production boxes.

Copy SSH key to remote machine.

::

    $ ssh-copy-id -i ~/.ssh/id_rsa rpi2

Ansible Config
--------------

Review my settings in *ansible.cfg* and *hosts* files in this repo. Edit them
as desired.

Bootstrap
---------

Bootstrapping pkgng on remote machine and then installing Python 2.7 is
required before the playbook can run. This is done outside of a playbook
because I like it that way. I have not tried running this in a playbook.

Thanks to
`Bootstrapping / installing pkg on FreeBSD unattended and without answering Yes <https://dan.langille.org/2013/12/06/bootstrapping-installing-pkg-on-freebsd-unattended-and-without-answering-yes/>`_
, use the following command to bootstrap pkgng.

::

    $ ansible --module-name raw --args "env ASSUME_ALWAYS_YES=YES /usr/sbin/pkg bootstrap" --become rpi2

Install Python2.7.

::

    $ ansible --module-name raw --args "/usr/sbin/pkg install -y python27" --become rpi2


Playbook
--------

Run the example playbook to install and configure a bunch of things.

::

    $ ansible-playbook playbook.yaml
