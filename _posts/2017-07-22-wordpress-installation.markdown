---
layout: post
title: WordPress - Installation
date: '2017-07-22 12:28:00'
tags:
- wordpress
- blogging
---

A quick guide on installing WordPress on an Ubuntu server.

Begin by downloading the latest WordPress image to the home directory.

    $ cd ~
    $ wget -c http://wordpress.org/latest.tar.gz

Extract the image

    $ tar -xzvf latest.tar.gz

Copy the extracted files to the root website folder

    $ sudo rsync -av wordpress/* /var/www/html/

Setup permissions

    $ sudo chown -R www-data:www-data /var/www/html/
    $ sudo chmod -R 755 /var/www/html/

Setup the default database for the WordPress server to use

    $ mysql -u root -p
    mysql> CREATE DATABASE wp_myblog;
    mysql> GRANT ALL PRIVILEGES ON wp_myblog.* TO <user>@'localhost' IDENTIFIED BY <password>;
    mysql> FLUSH PRIVILEGES;
    mysql> EXIT;

Setup a WordPress configuration file based off the sample provided.

    $ cd /var/www/html/ 
    $ sudo mv wp-config-sample.php wp-config.php
    $ sudo nano wp-config.php

Update with the database and user names, and user passwords

    define('DB_NAME', 'your_database_name');
    define('DB_USER', 'your_database_username');
    define('DB_PASSWORD', 'your_database_user_password');

Restart apache

    $ sudo systemctl restart apache2.service
    $ sudo systemctl restart mysql.service

Run the WordPress install script, by going to `WEBSITE/wp-admin/install.php` and following the instructions.

For completeness, configure Apache to show the WordPress main page by default.

    $ sudo nano /etc/apache2/mods-enabled/dir.conf

Adjust this file by moving `index.php` to the front of the list (so that apache defaults it to the default view)  
`DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`

References:  
[https://codex.wordpress.org/Installing\_WordPress](https://codex.wordpress.org/Installing_WordPress)

