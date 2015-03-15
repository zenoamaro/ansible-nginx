Nginx for Ansible
=================

A role for deploying and configuring [Nginx](http://nginx.com) and extensions on UNIX hosts using [Ansible](http://www.ansibleworks.com/).

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

Customizing Nginx site
-----------------------

The easiest way to customize your Nginx deployment
is to pass your own server configuration file for Nginx.
This can be done using Ansible templates.

Below is an example which

- Deploys a static HTTPS site on Nginx

- Site content is synced from Github repository using SSH

- Nginx server configuration file is generated from the supplied `site.conf` template

Related files and folders you need to have

- `keys/github` - Private key for [Github deployment key](https://developer.github.com/guides/managing-deploy-keys/)

- `keys/github.pub` - Public key for [Github deployment key](https://developer.github.com/guides/managing-deploy-keys/)

- `keys/example.crt` - SSL certificate (bundle) for your site, in OpenSSL format.

- `keys/example.key` - SSL private key.

- `roles/nginx` - This Ansible role, cloned as above.

- `site.conf` - your custom Nginx server configuration file, example below.

- `playbook.yml` - Deployment playbook. The playbook has been simplified for the example, as many file and folder locations are harcoded-

To run the playbook you need to pass the public HTTP/HTTPS IP address as a parameter:

    $ ansible-playbook -i yourinventoryfile --extra-vars "public_ip=1.2.3.4" playbook.yml

Playbook `playbook.yml`:
```
- hosts: example

  vars:
    nginx_create_default_site: no

  roles:
   - nginx

  pre_tasks:
    # Get apt up-to-date
    - name: APT update
      sudo: yes
      apt: update_cache=yes

    - name: Install required software for performing the deployment
      sudo: yes
      apt: name={{ item }} state=installed
      with_items:
        - git

    - name: Creates .ssh directory for root
      sudo: yes
      file: path=/root/.ssh state=directory

    # This public key is set on Github repo Settings under "Deploy keys"
    - name: Upload the private key used for Github cloning
      sudo: yes
      copy: src=keys/github dest=/root/.ssh/github

    - name: Correct SSH deploy key permissions
      sudo: yes
      file: dest=/root/.ssh/github mode=0600

    - name: Deploy site files from Github repository
      sudo: yes
      git: repo=git@github.com:miohtama/example.git dest=/srv/nginx/example key_file=/root/.ssh/github accept_hostkey=yes

    - name: Deploy SSL private key
      sudo: yes
      copy: src=keys/example.key dest=/root/example.key

    - name: Deploy SSL certificate
      sudo: yes
      copy: src=keys/example.crt dest=/root/example.crt

    - name: Set www-data file access right for the site
      sudo: yes
      file: path=/srv/nginx/example
            owner=www-data
            group=www-data
            mode=0770 recurse=yes
            state=directory

  post_tasks:
    # Produce a default site pointing to the shared folder
    - include: roles/nginx/tasks/site.yml
      template: site.conf
      site: example
      server_name: example.com
      access_log: /var/log/nginx/example.access.log
      error_log: /var/log/nginx/example.error.log
      document_root: /srv/nginx/example
      ssl_certificate: /root/example.crt
      ssl_certificate_key: /root/example.key
```

Then your Nginx configuration template, `site.conf`

```nginx
# Redirect all HTTP traffic to HTTPS
server {
    listen {{public_ip}}:80;
    server_name {{server_name}};
    rewrite ^/(.*) https://{{server_name}}/$1 permanent;
}

# Serve HTTPS traffic
server {

    listen {{public_ip}}:443;
    server_name {{server_name}};

    ssl on;
    ssl_certificate {{ssl_certificate}};
    ssl_certificate_key {{ssl_certificate_key}};

    access_log {{access_log}};
    error_log {{error_log}};
    root {{document_root}};
}
```

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