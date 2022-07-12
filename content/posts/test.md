---
title: "How I Manage My Servers With Ansible"
date: 2022-09-08T05:56:46+08:00
draft: false
---

## Table of Contents

[1. Introduction](#introduction)
[2. Requirements](#requirements)
[3. How it works](#how-it-works)
[4. Roles](#roles)
[5. Docker volume backups](#docker-volume-backups)
[6. Final notes](#final-notes)

## Introduction
In this blog post, I will show you how I manage my servers using Ansible.

I use two servers, one for production and one for development. I use them to host this personal website, and several servers for password management, note-taking, and so on.

<!--more-->

I already had docker-compose files for my containers, which may had done most of the job, and I have an automated installation script for my workstation, whose repository can be found [here](https://github.com/memreyagci/workstation-installation-script) and my blog post about it is [here](https://www.meyagci.com/how-i-manage-setup-my-workstation-shell-scripts-gnu-stow/).

However I wanted to take a more modern and universal approach for my servers. When writing a shell script, you need to take a lot of exceptions and different scenarios into consideration, write the appropriate output messages, and whatnot. On the other hand, Ansible handles them all in an elegant way. Moreover, if I decide to use more than one servers, managing them will be much more easier, especially when I start to use different operating systems.


## Requirements
- **Ubuntu 20.4 LTS** is what I chose as my server distro
- **Ansible**, for the host machine
- **[ansible-stow](https://github.com/caian-org/ansible-stow) module**, for deploying dotfiles
- **[ansible.posix](https://github.com/ansible-collections/ansible.posix) module**, for mounting block-storage devices, to use for Docker volume backups

## How it works
**For the servers**, I use the following startup script for installation, which creates a user called ansible that belongs to sudo group, and adds my ssh key:
```
#!/bin/sh

useradd -mG sudo -s /bin/bash -p $(echo $mypasswd | openssl passwd -1 -stdin) ansible

su -c "mkdir /home/ansible/.ssh" ansible

su -c "echo "$host_ssh" > /home/ansible/.ssh/authorized_keys" ansible
```
---
For the host machine, first, you need to clone the repository:
```
$ git clone --recurse-submodules https://github.com/memreyagci/server-configs
```
>Note: *"--recurse-submodules" is to clone turtl server into roles/containers/files, since it is a submodule of my server-configs repository.*

Thereafter, cd into server-configs and run the following command:
```
$ ansible-playbook $playbook --ask-become-pass --ask-vault-pass
```

> There are two playbooks: dev_servers.yml & prod_servers.yml

Here is a fast-forwarded screen capture:
![](ansible_server_installation.mp4)

## Roles
The configuration consists of the following files:

```
server-configs
├── dev_servers.yml
├── group_vars
│   ├── all.yml
│   ├── dev_servers.yml
│   ├── example_all.yml
│   ├── example_servers.yml
│   └── prod_servers.yml
├── prod_servers.yml
├── README.md
└── roles
    ├── containers
    │   ├── files
    │   └── tasks
    │       ├── baikal.yml
    │       ├── ghost.yml
    │       ├── main.yml
    │       ├── traefik.yml
    │       ├── turtl.yml
    │       └── vaultwarden.yml
    ├── cronjobs
    │   ├── files
    │   │   └── backup-docker-volumes.sh
    │   └── tasks
    │       └── main.yml
    ├── dotfiles
    │   └── tasks
    │       └── main.yml
    ├── mount
    │   └── tasks
    │       └── main.yml
    ├── packages
    │   └── tasks
    │       ├── docker.yml
    │       ├── general.yml
    │       └── main.yml
    └── users
        └── tasks
            └── main.yml
```

* **dev_server.yml & prod_server.yml** include the roles to run, as their names indicate they are run in different servers defined in /etc/ansible/hosts
* **group_vars**:
   * **all.yml** includes username and password of the account which will be used as the main user of the server (for ssh connections, container managemenet, etc.)
   * **dev_servers.yml & prod_servers.yml** consists of domains we want to use for the servers, also database passwords and admin tokens of some of the containers.
* **roles**:
   * **containers**: As the name suggests this role deploys the following Docker containers.
      * **Traefik**: A reverse proxy that deals with routing connections to other containers.
      * **Baikal**: CardDAV & CalDAV server
      * **Ghost**: A blogging platform
      * **Turtl**: Note-taking server
      * **Vaultwarden**: Password management server
   * **cronjobs**: It adds a script I wrote called backup-docker-volumes to cron.
   * **dotfiles**: Deploys my dotfiles using GNU Stow
   * **mount**: Adds the block-storage device to the fstab and mounts it.
   * **packages**: Installs necessary packages for the server. Some of them are: rsync, Docker, neovim, stow, python3-pip..
   * **users**: Creates a user of sudo and docker groups for main usage.


## Docker volume backups
Let's take a look at the script I wrote for Docker volume backups:
```
#!/bin/sh

CONTAINERS=(
    baikal
    ghost
    traefik
    turtl-db
    turtl-server
    vaultwarden
)

VOLUMES=(
    baikal_config
    baikal_data
    ghost
    letsencrypt
    traefik
    turtl
    vaultwarden
    )

docker stop ${CONTAINERS[@]}

rsync -vargo ${VOLUMES[@]} /mnt/blockstorage/docker-volume-backups
cd docker-volume-backups
tar cvf docker_volumes_backups_$(date '+%d-%m-%Y').tar *

docker start ${CONTAINERS[@]}
```
It's a basic script that I come up with in a short time. It stops all the containers, syncs the volumes into a folder in blockstorage, archives them, and finally starts the containers again.

It is crucial to use -g and -o flags for rsync, which preserves group and user ownerships. Otherwise, you may have lots of issues about permissions when you try to recover them.

## Final Notes
Ansible is really a great software. I will  probably be using it for my workstations too. As a matter of fact, the previous post was meant to be "How I manage my workstations with Ansible", however some errors related to pacman occured during tests, probably nothing unsolvable, but I felt like writing a shell script at the moment and went with it. Though I will definetly give it another try.

Having a central system you can use to manage your machines, without having to think of every little scenario for every piece of software you use, and being able to do those in a distro-agnostic way is awesome.
