---
layout: post
title: The website foundation - LAMP stack
date: '2017-06-22 10:42:00'
tags:
- hosting
---

 **tl;dr** a simple guide to setting up a LAMP stack on an existing Ubuntu server.

For my purposes I'm referring to LAMP as:  
**L** inux (Ubuntu LTS): _Operating system_  
**A** pache: _webserver_  
**M** ySQL: _database_  
**P** HP: _scripting language_

**Linux:**  
see alternate post

**Apache:**  
Install Apache from the Ubuntu repositories

    $ sudo apt-get install apache2

Configure Apache to start automatically

    $ sudo systemctl enable apache2
    $ sudo systemctl start apache2
    $ sudo systemctl status apache2

Test Apache by opening the server IP address in a web-browser  
`server_ip`  
If everything went as expected you should see a generic page describing the Apache install and configuration files.

**MySQL:**  
Install MySQL and set the root password

    $ sudo apt-get install mysql-server mysql-client

Check the status of the service

    $ sudo systemctl status mysql

Clean out some insecure default permissions

    $ mysql_secure_installation

**PHP:**  
Update the repositories, then install PHP and associated packages

    $ sudo apt-get update
    $ sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql

Check that everything installed correctly by testing the current version of php that is installed (a version number should be displayed).

    $ php -v

Once satisfied that everything has installed correctly, lets create a test page to confirm PHP is playing nice with Apache.

    $ sudo nano /var/www/html/test.php

Enter the following code and save  
`<?php phpinfo(); ?>`  
Save the file, then restart Apache

    $ sudo systemctl restart apache2

Navigate to the page we just created  
`server_ip/test.php`  
If everything works as expected we should see some details on the PHP installation.  
Once satisfied everything is working as expected, remove the page just created.

    $ sudo rm /var/www/html/test.php

Congratulations, you've now got a server setup and a LAMP stack installed.

