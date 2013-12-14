Nginx for Ansible
=================

A role for deploying and configuring [Nginx](http://nginx.com) and extensions on unix hosts using [Ansible](http://www.ansibleworks.com/).

It can additionally be used as a playbook for quickly provisioning hosts.

Vagrant machines are provided to produce a boxed install of Nginx or a VM for integration testing.


Supports
--------

Supported Nginx versions:

- Nginx 1.4
- Nginx 1.x (untested)

Supported targets:

- Ubuntu 12.04 "Precise Pangolin"
- Ubuntu LTS (untested)
- Debian (untested)

Installation methods:

- Binary packages from the official repositories at [nginx](http://wiki.nginx.org/Install)

Callable tasks:

- `site`: Creates and enables (or removes) a nginx site


Usage
-----

Clone this repo into your roles directory:

    $ git clone https://github.com/user/ansible-nginx.git roles/nginx

And add it to your play's roles:

    - hosts: ...
      roles:
        - nginx
        - ...

This roles comes preloaded with almost every available default. You can override each one in your hosts/group vars, in your inventory, or in your play. See the annotated defaults in `defaults/main.yml` for help in configuration. All provided variables start with `nginx_`.

You can also use the role as a playbook. You will be asked which hosts to provision, and you can further configure the play by using `--extra-vars`.

    $ ansible-playbook -i inventory --extra-vars='{...}' main.yml

To provision a standalone Nginx box, start the `boxed` VM, which is a Ubuntu 12.04 box:

    $ vagrant up boxed

You will find Nginx listening on the VM's on address `192.168.33.20` in the private network. You can then connect to it as usual. You will want to customize it so to serve your sites and assets.

Run the tests by provisioning the appropriate VM:

    $ vagrant up test-ubuntu-precise

At the moment, `ubuntu-precise` is the only test VM available.


Still to do
-----------

- Support other distros
- Add a library of modules (if possible)
- Add useful snippets for tuning, proxying, and writing virtualhosts
- Add quick site definition and shared folder for boxed instances


Changelog
---------

### 0.1

Initial version.
