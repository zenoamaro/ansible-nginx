Nginx for Ansible
=================

A role for deploying and configuring [Nginx](http://nginx.com) and extensions on unix hosts using [Ansible](http://www.ansibleworks.com/).

It can additionally be used as a playbook for quickly provisioning hosts.

Vagrant machines are provided to produce a boxed install of Nginx or a VM for integration testing.


Supports
--------
Supported Nginx versions:
- Nginx 1.6.x
- Nginx 1.5.x
- Nginx 1.4.x

Supported targets:
- Ubuntu 14.04 LTS "Trusty Tahr"
- Ubuntu 12.04 LTS "Precise Pangolin"
- Debian (untested)

Installation methods:
- Binary packages from the official repositories at [nginx](http://wiki.nginx.org/Install)

Callable tasks:
- `site`: Creates and enables (or removes) a nginx site


Usage
-----
Clone this repo into your roles directory:

    $ git clone https://github.com/zenoamaro/ansible-nginx.git roles/nginx

And add it to your play's roles:

    - hosts: ...
      roles:
        - nginx
        - ...

It is recommended that you pin your NGINX version by setting `nginx_version` to something like `1.6.2`, `1.6.*` or even `1.*`.

This roles comes preloaded with almost every available default. You can override each one in your hosts/group vars, in your inventory, or in your play. See the annotated defaults in `defaults/main.yml` for help in configuration. All provided variables start with `nginx_`.

The role provides a default fallback site which returns 404 to all requests not matched by other rules. You can set `nginx_create_default_site` to `false` to disable it.

You can also use the role as a playbook. You will be asked which hosts to provision, and you can further configure the play by using `--extra-vars`.

    $ ansible-playbook -i inventory --extra-vars='{...}' main.yml

To provision a standalone Nginx box, create a `~www` directory (by default) with the contents you wish to serve, and start the `boxed` VM, which is a Ubuntu 14.04 box:

    $ vagrant up boxed

You will find Nginx listening on the VM's on address `192.168.33.20` in the private network. You can then connect to it as usual. You will want to customize it so to serve your sites and assets.

Run the tests by provisioning the appropriate VM:

    $ vagrant up test-ubuntu-trusty

At the moment, the following test boxes are available:

- `test-ubuntu-precise`
- `test-ubuntu-trusty`


Still to do
-----------
- Support other distros
- Add a library of modules (if possible)
- Add useful snippets for tuning, proxying, and writing virtualhosts
- Add quick site definition and shared folder for boxed instances


Changelog
---------
### 0.2
- Added version pinning. Currently tracking `v1.6.x`.

### 0.1.1
- The package list is not being updated in playbooks anymore.
- Added more test machines.

### 0.1
Initial version.


License
-------
The MIT License (MIT)

Copyright (c) 2015, zenoamaro <zenoamaro@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.