---
title: "Development CLI"
description: ""
lead: "Set up a development environment using the Development command line interface"
date: 2021-02-16T17:51:10Z
lastmod: 2021-02-16T17:51:10Z
draft: false
images: []
contributors: [Steve Blamey]
---

The [uzERP development CLI](https://github.com/uzerpllp/uzerp-dev-cli) provides an easy way to get a local ERP development or demo environment up and running quickly.

## Features

* Runs uzERP and associated software in containers
* Uses podman to run containers in a pod, where they communicate on localhost
* Provides convenience commands:
    * Set a local IP for Xdebug to connect back to when debugging PHP
    * Create and execute phinx migrations in the uzERP container
    * Run Composer install and update inside the uzERP container

## Installation and Set-up

### Podman

The uzERP dev CLI uses podman to run the required containers. On Fedora podman is in the yum repos, simply `dnf install podman`.

On Ubuntu 20.04, podman can be installed from the repos maintained by Kubic:

```
$ . /etc/os-release

$ sudo sh -c "echo 'deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /' > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list"

$ curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/Release.key | sudo apt-key add -

$ sudo apt-get update -qq

$ sudo apt install podman fuse-overlayfs
```

### uzERP Development Commandline

Before installing this package, install some additional requirements:

* pip, used to install python packages
* git, required to get the uzERP source code
* npm, needed to build uzERP front-end assets

```
$ sudo apt install python3-pip git npm
```

On Ubuntu 20.04, edit your `.bashrc` file to ensure that the path for user-binaries is set by appending the following:

```
export PATH="$(systemd-path user-binaries):$PATH"
```

Close the terminal session and start a new one, then install the CLI package:

```
$ pip3 install --user https://github.com/uzerpllp/uzerp-dev-cli/archive/master.zip
```

### Set-up uzERP

Download the uzERP source:

```
$ cd ~/
$ git clone https://github.com/uzerpllp/uzerp.git
```

Start uzERP. This will create configuration files and data directories, create the container pod and bootstrap the databases.

```
$ cd uzerp
$ uzerp up
```

Install PHP dependencies, build the frontend assets and prepare a uzERP config file.

```
$ uzerp composer install
$ npm install
$ npm run gulp
$ cp conf/config-example.php conf/config.php
```

Edit config.php. Change database name to 'uzerp' and host to 'localhost'.

Set an ACL on the data dirs, so that www-data inside the container can create directories/files:

```
$ setfacl -R -m "u:100032:rwx" ~/uzerp/data
```

Browse to http://localhost:8080 and log in to uzERP with the default password username and password admin/admin

## CLI Commands

For help on available commands, use the help facility:


```
$ uzerp --help
```

## Tips and Tricks

See a list of the running containers:

`$ podman ps`

Inspect the postgresql log:

`$ podman log uzerp-postgres`
