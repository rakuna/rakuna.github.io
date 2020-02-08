---
layout: post
title: 'Expanding functionality: Docker & Discourse'
---

# Commenting is a fun feature, let's set it up in a way that protects privacy (Discourse vs. disqus)


# Setup the server
https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#create-new-cloud-server
Another lightsail server 1GiB RAM
Which we'll also use as a test bed for restoring our previously created back-ups to.
Ensure we open https connections (to port 443)

# Install git / docker
Following the discourse [tutorial](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md)
https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#create-new-cloud-server


Install Git
```
sudo apt update
sudo apt install git
```

https://docs.docker.com/install/linux/docker-ce/ubuntu/
Prepare for the Docker install

Remove any old installs
```
$ sudo apt remove docker docker-engine docker.io
```
Install packages to allow apt to use a repository over https
```
$ sudo apt update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
Add Docker's official GPG key:
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
Verify that you now have the key with the correct fingerprint
```
$ sudo apt-key fingerprint 0EBFCD88

pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```
Setup the stable repository
```
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

Install Docker
```
$ sudo apt update
$ sudo apt install docker-ce
$ sudo docker run hello-world
```

# Discourse install
```
sudo -s
mkdir /var/discourse
git clone https://github.com/discourse/discourse_docker.git /var/discourse
cd /var/discourse
```


```
./discourse-setup

Hostname: discourse.libreengineer.com
Email: tom@libreengineer.com
SMTP: mail.libreengineer.com
Port: [587]
Username: tom@libreengineer.com
Password: ####################
Let's encrypt email: mccleery.tom@gmail.com
```

Discourse will then run for a few minutes (through what it call bootstrapping).
Once completed, discourse is installed.

# Discourse configuration
Once installed some admin settings need to be registered.
https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#start-discourse





https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md


https://docs.docker.com/get-started/
https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md
http://stroupaloop.com/blog/discourse-setup-using-aws/
https://scotch.io/tutorials/getting-started-with-docker


# Embedding discourse into ghost
Involves the editing of your theme to include the discourse embedding code.