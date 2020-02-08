---
layout: post
title: Back-up & Restore Ghost
---

Ghost currently has no back-up functionality that is exposed to the command line (but there's an open github [issue](https://github.com/TryGhost/Ghost-CLI/issues/468) looking into this feature).
Until then, we can back-up by taking a copy of our MySQL database and ghost content directory.

# Back-up
Our back-up is made of three key stages:
1. MySQL data back-up
2. Ghost content folder back-up
3. Sending the data off-server

Which we'll then automate through cron.

## MySQL
The first part of our back-up is saving a copy of our ghost database.
### Ghost database
To run a back-up, we need to know what we're backing up.
So lets find the exact name of our ghost database.
```
$ mysql -u root -p -e 'show databases;'
Enter password:
+--------------------+
| Database           |
+--------------------+
| information_schema |
| ghost_prod         |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```
Which tells us that `ghost_prod` is the name of our database.

### Dedicated user
Applying the [principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) we need a dedicated MySQL user, which we'll call `ghost-backup`, with a limited set of [permissions](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html), in particular [SELECT](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_select) (read) and [LOCK TABLE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_lock-tables) (avoid database updates whilst running our backup) to run our backup, instead of the (unlimited) permissions of the database root user. This has the side benefit of avoiding nasty accidents. For example, if we make a mistake and attempt to [DROP](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_drop) (delete) our database in the back-up process it won't be possible, because we don't have those permissions.
https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql
https://serverfault.com/questions/22712/minimum-permissions-for-a-user-to-perform-a-mysqldump?newreg=f8c657a814ac420f9fdf0b6bbb7f6202

To create this user we'll use our MySQL root user account (the one we're not wanting to embed into our back-up).
Login:
```
$ mysql -u root -p
Enter password:
```
Create user:
*(replacing [password] with our login credential)*
```
mysql> CREATE USER 'ghost-backup'@'localhost' IDENTIFIED BY '[password]';
```
Grant the required permissions:
```
mysql> GRANT SELECT, LOCK TABLES ON ghost_prod.* to 'ghost-backup'@'localhost';
```
To confirm everything ran as expected, lets check the permissions granted to our backup user.
```
mysql> SHOW GRANTS FOR 'ghost-backup'@'localhost';
+---------------------------------------------------------------------------+
| Grants for ghost-backup@localhost                                         |
+---------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO 'ghost-backup'@'localhost'                          |
| GRANT SELECT, LOCK TABLES ON `ghost_prod`.* TO 'ghost-backup'@'localhost' |
+---------------------------------------------------------------------------+
```
Note: The [USAGE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_usage) privilege means no privilege.

### Test back-up
Let's check all our MySQL back-up configuration with a simple test
```
$ mysqldump --user=root -p ghost_prod > /tmp/[timestamp]-ghost-backup.sql
Enter password:
```
Which should create a back-up file in the `/tmp` directory.

### MySQL back-up code
Let's now compile our backup into a script executable format:
Which has our back-up user's password embedded (which was the whole reason to create the separate user).
`mysql --user=ghost-backup --password=[password] ghost_prod > /tmp/ghost_prod-[timestamp].sql`

## Ghost content
The other half of the back-up is the ghost content folder.
### tar
[tar (**t**ape **ar**chive)](https://en.wikipedia.org/wiki/Tar_%28computing%29) is a convenient program for compiling a number of files into a single file. The original purpose being to allow this single file to be written to a tape archive effectively and efficiently.

For our purposes we'll use tar to compile the ghost content directory into a single file.

tar has some a [long list of options](https://www.gnu.org/software/tar/manual/html_section/tar_22.html#SEC42) so it is worth finding the key/common options.

For our purposes the main options are:
* -c Create an archive
* -z Compress with Gzip
* -v Verbose, say everthing that is happening
* -f Specify the archive file
For example, to create an archive of the current directory and compress it with Gzip:
```
$ tar -cvz -f test.tar.gz ./*
```
### Ghost files
To archive our Ghost content directory we simply point to our Ghost directory
```
$ tar -cvz -f ghost-content.tar.gz [path to ghost directory]
```

## Final archive
So far we've created two files:
1. MySQL back-up
2. Ghost content directory back-up

To make it easier to manage the parts of our back-up (and ensure we always have both parts when we want to restor) let's put these together into a single file.
This can be done by running another tar command:
```
$ tar -cvz -f ghost-archive.tar.gz [path to ghost content tar] [path to MySQL dump file]
```

## Off-site
Now that we've created our archive file we need somewhere to store it.
Keeping it on the server may work for accidental changes that we want to revert, but not for a complete server failure. So we want to store (at least some of) our archive files somewhere other than the server.

### Find a back-up server
We can store these files in a variety of places, however in keeping with the AWS approach that we've been using to build our other apps, we'll use the [Amazon Simple Storage Service (S3)](https://aws.amazon.com/s3/) to store our back-up archives.

https://d1.awsstatic.com/whitepapers/Backup_and_Recovery_Approaches_Using_AWS.pdf

### Install the AWS CLI
We'll use the [AWS command line interface (CLI)](https://aws.amazon.com/cli/) tool for simplicity. Which is written in python and installable through [pip](https://pypi.org/project/pip/).

#### Install pip
Check if pip is installed:
```
$ pip --version
```
If pip is not installed, we can install it by following the [PyPA (Python Packaging Authority)](https://www.pypa.io/en/latest/) [installation guide](https://pip.pypa.io/en/stable/installing/).
```
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python get-pip.py
```
To confirm that we're using the latest version of pip, we can upgrade it via pip:
```
$ pip install --upgrade pip
```

#### Install the AWS CLI via pip
```
$ pip install awscli --upgrade --user
```
Verify that the AWS CLI installed correctly
```
$ aws --version
```
To upgrade to the AWS CLI we can run:
```
$ pip install awscli --upgrade --user
```

### Configure AWS CLI
To minimise the damage a compromised AWS CLI could do to our AWS account and S3 buckets, we'll create a dedicated IAM user for running AWS CLI commands.
We'll then restrict it to only the commands it absolutely needs (in our case to be able to put files into the S3 backup folder).

1. Create a new user in AWS Identity and Access Management (IAM).
2. Apply the AWS managed S3FullAccess permissions for now
    * We'll restrict this later to only put object permissions for the backup area of our s3 bucket
3. Create the Access keys
    * Which we need for configuring the AWS CLI

Configure the AWS CLI:
* Insert our newly created IAM credentials into the Access Key fields; and
* Choose a default region *(e.g. where you bucket is stored)*.
```
$ aws configure
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]:
Default output format [None]:
```
We can now test that everything is running as expected
```
$ touch test
$ aws s3 cp test s3://[bucket]/backup/
upload: ./test to s3://[bucket]/backup/test
```
Which should create a file called test in the backup folder of our s3 bucket

### automated back-up
`$ aws s3 cp [backup file] s3://[bucket]/backup/`


https://aws.amazon.com/getting-started/tutorials/backup-to-s3-cli/
https://stackoverflow.com/questions/17832860/backup-strategies-for-aws-s3-bucket
### Add to the script the production code


## Back-up management rules
### Clean the local back-up folder
We'll use tmpreaper to clean our local back-ups
https://www.thegeekstuff.com/2013/10/tmpreaper-examples/

### Move back-ups into progressively cheaper storage options
S3
Standard -> Infrequent Access -> Glacier

### Delete S3 files based on a back-up rule
e.g. Apple time machine back-up system which according to [Wikipedia](https://en.wikipedia.org/wiki/Time_Machine_(macOS)#Overview):
>Time Machine saves hourly backups for the past 24 hours, daily backups for the past month, and weekly backups for everything older than a month until the volume runs out of space. At that point, Time Machine deletes the oldest weekly backup. 

# Restore
What good is a back-up that we don't confirm will work when we need it?

## Untar

## Content restore

## MySQL restore
https://stackoverflow.com/questions/105776/how-do-i-restore-a-dump-file-from-mysqldump
```
$ mysql -u root -p

mysql> create database mydb;
mysql> use mydb;
mysql> source db_backup.dump;
```


# References
https://ericmathison.com/blog/backup-your-ghost-blog-to-amazon-s3/
https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql
https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html
