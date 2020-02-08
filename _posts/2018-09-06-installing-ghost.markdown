---
layout: post
title: Installing Ghost
date: '2018-09-06 12:42:57'
tags:
- blogging
- hosting
---

Now that my, new, [Lightsail server](https://libreengineer.com/lightsail) is up and running, it's time to put it to good use, by installing the [Ghost](https://ghost.org/) publishing (blogging) platform.

_This guide is adapted from the [Ghost documentation](https://docs.ghost.org/docs/install#section-server-setup)._

For simplicity Ghost has only one recommended stack (server configuration), which I've chosen to implement:

- [Ubuntu long term support (LTS)](https://wikipedia.org/wiki/Ubuntu_(operating_system)) 16.04LTS or 18.04LTS (as at 2018) as the operating system
- [MySQL](https://wikipedia.org/wiki/MySQL) as the database
- [nginx](https://wikipedia.org/wiki/Nginx) (minimum version 1.9.5 for SSL) as the webserver
- [systemd](https://wikipedia.org/wiki/Systemd)
- [Node](https://en.m.wikipedia.org/wiki/Node.js) ([Recommended version](https://docs.ghost.org/docs/supported-node-versions) installed via NodeSource) for server-side scripting
- A server with at least 1GiB memory (RAM + swap)
- A (non-root) user for running Ghost command line interface (CLI) commands

**a (non-root) user:** [user]  
Which we'll use to run all our commands.

    ~$ sudo adduser [user]
    ~$ sudo usermod -aG sudo [user]
    ~$ su - [user]

**Server memory:**  
As this server has less than 1GiB RAM (512MiB to be precise) I'll need to configure some swap memory to get Ghost running correctly. 1GiB of swap (double our RAM, and the minimum memory recommended by Ghost) should be sufficient.  
_Without this, Ghost was unable to install and crashed during the install process._

Check there is no swap already configured (which there shouldn't be)

    ~$ free -h
                  total used free shared buff/cache available
    Mem: 486M 317M 7.2M 5.5M 162M 131M
    Swap: 0B 0B 0B

Which should show a 0B (as above) total swap.  
Now allocate space (1GiB) to a file that we'll use as the swap.

    ~$ sudo fallocate -l 1G /var/swap

Turn the file into a swap file

    ~$ sudo mkswap /var/swap

Restrict the permissions so that only the root user can read the swap file contents.

    ~$ sudo chmod 600 /var/swap

Turn the swap file on

    ~$ sudo swapon /var/swap

Enable the swap file by default after each reboot

    ~$ sudo echo '/var/swap swap swap defaults 0 0' | sudo tee -a /etc/fstab

Installation and configuration of the required software for Ghost.  
Ensure all packages are up to date:

    ~$ sudo apt update
    ~$ sudo apt upgrade

**nginx:**

    ~$ sudo apt install nginx
    ~$ sudo ufw allow 'Nginx Full'

**MySQL:**

    ~$ sudo apt install mysql-server

MySQL comes with some non-secure default settings which we should tidy up

    ~$ mysql_secure_installation

**Node:**  
Determine the recommended version of Node from the [Ghost documentation](https://docs.ghost.org/docs/supported-node-versions)  
Install the current LTS (as at 2018)

    ~$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash
    ~$ sudo apt install -y nodejs

Alternate commands can be found from [nodejs](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

**Ghost CLI:**  
Install the ghost command line interface (CLI) which will be used for all other Ghost configuration:

    ~$ sudo npm i -g ghost-cli

Create the folder where we want to install Ghost, and align its permissions.

    ~$ sudo mkdir -p /var/www/ghost
    ~$ sudo chown [user]:[user] /var/www/ghost
    ~$ sudo chmod 775 /var/www/ghost/

**Ghost:**  
Install ghost to the folder we setup

    ~$ cd /var/www/ghost/
    /var/www/ghost$ ghost install

Follow the [prompts](https://docs.ghost.org/docs/cli-install#section-prompts) to complete the installation.

Note:  
If you haven't setup a hostname, you can use a static IP `http://[static-ip]`  
If you setup https, then port 443 needs to be open on our cloud firewall.

Ghost should now be up and running and accessible at your hostname.

### References:

[https://docs.ghost.org/docs/install#section-server-setupcreating](https://docs.ghost.org/docs/install#section-server-setupcreating)  
[https://docs.ghost.org/docs/install#section-server-setupcreating](https://docs.ghost.org/docs/install#section-server-setupcreating)  
[https://docs.ghost.org/docs/hosting#section-adding-swap-memory](https://docs.ghost.org/docs/hosting#section-adding-swap-memory)  
[https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04)  
[https://docs.ghost.org/docs/cli-install#section-prompts](https://docs.ghost.org/docs/cli-install#section-prompts)

