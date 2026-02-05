---
title: "how to setup FileRun w/ Hetzner Storage Box"
description: "a step-by-step tutorial on how to setup your own Google Drive-esque file server"
date: "Apr 1 2025"
---

The order in which you want to do this:

1.  Install & start Nginx
2.  Install, start, setup MariaDB
3.  Install & configure Php

Linux Commands for Reference
============================

*   CTRL + X -> Exit a file in terminal editor
*   :qa! -> quit a vim file without saving
*   :wq -> save & quit a vim file
*   rm -> delete a file
*   rm -r -> delete a directory and all sub-files
*   mv {file} {/path/to/loc} -> move a file to a new location
*   mv {original\_name} {ne\_name} -> rename a file
*   mv {/folder\_path/} {/path/to/new/} -> move a folder to a new location

Update Software Packages
------------------------

sudo apt update
sudo apt upgrade

Install Apache & PHP
--------------------

1. Get all the PHP repositories

```sudo add\-apt-repository ppa:ondrej/php -y```

2. Then run the latest FileRun PHP configuration: [https://docs.filerun.com/php\_configuration](https://docs.filerun.com/php_configuration)

### Verify that Apache is working

Create an info.php file, this allows you to check the status of your install

```sudo nano /var/www/html/info.php```

Paste this in the contents & save the file

```
<?php
phpinfo();
```

Now navigate to `IP\_ADDRESS/info.php` and you should see a page with a information about your PHP install. Verify the PHP version matches the supported version by FileRun.

Add Permissions
---------------

Give permission to PHP to make changes to the install folder
```sudo chown -R www-data:www-data /var/www/html/```

Setup MySQL DB
--------------

From: [https://docs.filerun.com/install-ubuntu-20-ngnix#step\_3\_-\_install\_mariadb\_database\_server](https://docs.filerun.com/install-ubuntu-20-ngnix#step_3_-_install_mariadb_database_server)

Now that we have our web server up and running, it is time to install a database server. This server will be managing the FileRun database which holds the application settings, the users settings and information about your files.

With two simple commands the database server MariaDB will be installed and running:
```sudo apt install mariadb-server mariadb-client```

If it’s not running, start it with this command:
```sudo systemctl start mariadb```

To enable MariaDB to automatically start at boot time, run
```sudo systemctl enable mariadb```

Now run the post installation security script.
```sudo mysql\_secure\_installation```

The prompt will ask you for your current MariaDB root password. Since you just installed MariaDB, you won’t have one, so leave it blank by pressing `ENTER`. Then the prompt will help you set a password:

```
Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorization.

Set root password? \[Y/n\] y
New password: PASSWORD
Re-enter new password: PASSWORD
Password updated successfully!
Reloading privilege tables..
 ... Success!
```

For the rest of the questions, you should simply hit the `ENTER` key through each prompt to accept the default values. This will remove some sample users and databases, disable remote root logins, and load these new rules so that MariaDB immediately respects the changes we have made.

Setup Domain
------------

In your hosting provider, create an A record and point it to your IP address.

Then, edit the `000-default.conf` file

```sudo nano /etc/apache2/sites-available/000\-default.conf```

Modify the configuration to reflect your domain settings. Update the `**ServerName**`, `**ServerAlias**`, and `**DocumentRoot**` as follows:

```
<VirtualHost \*:80\>
   ServerAdmin webmaster@yourdomain.com
   ServerName yourdomain.com
   ServerAlias www.yourdomain.com
   DocumentRoot /var/www/html
   ErrorLog ${APACHE\_LOG\_DIR}/error.log
   CustomLog ${APACHE\_LOG\_DIR}/access.log combined
</VirtualHost>
```

Access your domain in the browser from `http://yourdomain.com` and ensure the index page loads.

Setup SSL Certificate
---------------------

Install CertBot

sudo apt install certbot python3\-certbot-apache

Generate the SSL certificate

sudo certbot --apache -d yourdomain.com

Access your domain in the browser from `https://yourdomain.com` and ensure the index page loads and you have the SSL connection

Install IonCube Loader
----------------------

From: [https://docs.filerun.com/ubuntu\_20\_-\_apache#step\_2\_configuring\_php](https://docs.filerun.com/ubuntu_20_-_apache#step_2_configuring_php)

The following command will install the PHP modules needed by FileRun:

sudo apt-get install php-mbstring php-zip php-curl php-gd php-ldap php-xml php-imagick php-mysql

One last module which needs manual install is `ionCube`:

Download the package (Linux 64 bit):

sudo wget https://downloads.ioncube.com/loader\_downloads/ioncube\_loaders\_lin\_x86\-64.tar.gz

And extract it

sudo tar -xzf ioncube\_loaders\_lin\_x86\-64.tar.gz -C /usr/lib/php

Now, let’s load PHP with the downloaded extension:

sudo vim /etc/php/{PHPVERSION}/apache2/conf.d/00\-ioncube.ini

Replace `{PHPVERSION}` with the current PHP version (i.e: `8.2`)

And paste the following inside:

zend\_extension = /usr/lib/php/ioncube/ioncube\_loader\_lin\_{PHPVERSION}.so

Replace `{PHPVERSION}` with the current PHP version (i.e: `8.2`)

Restart Apache

sudo systemctl restart apache2

Go to IPADDRESS/info.php in your browser & make sure PHP is still working.

With the ionCube extension installed, let’s create a file which will automatically get appended by PHP to its configuration. This will include all the settings needed by FileRun.

Access your domain in the browser from `[https://yourdomain.com](https://yourdomain.com)` and ensure the index page loads and you have the SSL connection

Setup PHP config
----------------

Let’s create a file which will automatically get appended by PHP to its configuration. This will include all the settings needed by FileRun.

sudo vim /etc/php/{PHPVERSION}/apache2/conf.d/filerun.ini

Replace `{PHPVERSION}` with the current PHP version (i.e: `8.2`)

Follow the latest configuration here: [https://docs.filerun.com/php\_configuration](https://docs.filerun.com/php_configuration) to setup the contents of the file.

**Note:** Don't use any paths located in /usr/ in this file, for security reasons

Restart Apache

sudo systemctl restart apache2

Access your domain in the browser from `[https://yourdomain.com](https://yourdomain.com)` and ensure the index page **and** `/index.php` loads and you have the SSL connection

If after this step, your PHP pages are broken, comment out the `open_basedir` line in the file, then restart Apache

Install FileRun
---------------

From: [https://docs.filerun.com/ubuntu\_20\_-\_apache#step\_3\_installing\_filerun](https://docs.filerun.com/ubuntu_20_-_apache#step_3_installing_filerun)

Clean the default files from the root folder of your webserver (`/var/www/html/`):

cd /var/www/html/
sudo rm index.html
sudo rm info.php

Download the FileRun installation zip archive from the FileRun client account: https://filerun.com/client-area

sudo wget <url\_of\_download\> 

Install `unzip`:

sudo apt-get install unzip

Extract the downloaded FileRun archive:

sudo unzip FileRun.zip

Make Apache the owner of the folder so that it can make change:

sudo chown -R www-data:www-data /var/www/html/

Open your browser and point it to `https://yourdomain.com`

From here, you just have to follow the web installer, which will help you get FileRun running with just a few clicks.

Setup Hetzner Storage box
-------------------------

Stop the Apache server

sudo systemctl stop apache2

From: [https://docs.hetzner.com/storage/storage-box/backup-space-ssh-keys/](https://docs.hetzner.com/storage/storage-box/backup-space-ssh-keys/)

1) Enable SSH access in your Hetzner Storage Box account

2) Generate an SSH key in the terminal

ssh-keygen

Copy the name of the public key. It's usually `id_rsa.pub` but sometimes it can be `id_<numbers>.pub`

3) Connect to the Hetzner Server Box sever & upload your SSH key

cat ~/.ssh/id\_rsa.pub | ssh -p23 uXXXXX@uXXXXX.your-storagebox.de install-ssh-key
uXXXXX@uXXXXX.your-storagebox.de's password:

Replace `id_rsa.pub` with the name of your public key, if it's different

4) Install required packages

`sudo apt install sshfs -y`

5) Create mount directory

sudo mkdir /mnt/storagebox

6) Update fstab file

sudo nano /etc/fstab

7) Add the following command to the file to ensure it mounts on boot

uXXXXX@uXXXXX.your-storagebox.de:/ /mnt/storagebox fuse.sshfs \_netdev,allow\_other,default\_permissions,uid=33,gid=33 0 0

8) Reload mount

sudo mount -a

9) Ensure permissions are given

ls -lah /mnt/storagebox

10) Restart Apache server

sudo systemctl start apache2

Now you have a FileRun server setup with PHP & Apache, configured with a domain name and SSL certificate, and connected to Hetzner Storage Box!
