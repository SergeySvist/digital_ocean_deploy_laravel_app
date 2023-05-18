## Create your droplet on DigitalOcean

### Recommendations:

1. Ubuntu 20+
2. Choose Frankfurt as a datacenter region
3. Use SSH instead password

## Connect to Droplet as root

1. Connect via ssh, disconnect and try again.

```properties
# host

ssh root@167.172.187.96
```
```properties
# on droplet as root

exit
```
```properties
# host

ssh root@167.172.187.96
```

## Create a new admin user

1. ### Create a new ssh key on your host for secure connection as new admin user

```properties
# host

ssh-keygen -t rsa -b 4096 -C "digitaloceansilver"
```

Enter file in which to save the key, for example **/Users/silver/.ssh/id_digitaloceansilver**

2. ### Copy the public ssh key in the clipboard like so
```properties
# host

cat id_codecontinueserver.pub | pbcopy
```

3. ### Create a new user
```properties
# on droplet as root

adduser silver
```

4. ### Try to login as a new user
```properties
# on droplet as root

sudo su silver
```

5. ### Provide sudo privilege to new user
Login as root (if you are logged in as silver type *exit*)

```properties
# on droplet as root

usermod -aG admin silver

# try login
sudo su silver
# cd to silver home directory
cd
# login as root
sudo su root
# login as silver
sudo su silver

# from home direcotry as silver
mkdir .ssh
cd .ssh
vim authorized_keys

# paste the public key string (that was created early on the host)
# save and quit from vim (:wq)
```

Exit from droplet and try to connect as silver

```properties
# host

ssh -i ~/.ssh/id_digitaloceansilver silver@167.172.187.9
```

## Install PHP 8, Apache2, MySql

Install PHP, Apache
```properties
# on droplet as silver

# Refresh indexes
sudo apt update

sudo apt install software-properties-common

# Add PHP Repository
sudo add-apt-repository ppa:ondrej/php

# Press ENTER to enable the repository

# Refresh indexes
sudo apt update

# Install PHP 8 on Ubuntu 20.04 LTS
sudo apt install php8.0

# The command installs apache2 and libapache2-mod-php8.0 in case it's not installed
# The command will also install libapache2-mod-php8.0 in case Apache is already installed

# Verify PHP
php --version

# Output
PHP 8.0.3 (cli) (built: Mar  5 2021 07:54:13) ( NTS )
Copyright (c) The PHP Group
Zend Engine v4.0.3, Copyright (c) Zend Technologies
    with Zend OPcache v8.0.3, Copyright (c), by Zend Technologies

# Autoclean
sudo apt autoclean

# Autoremove
sudo apt autoremove

# Server Status
sudo systemctl status apache2

# Output
â— apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-08-24 14:53:05 UTC; 4min 14s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 11595 (apache2)
      Tasks: 6 (limit: 1136)
     Memory: 11.0M
     CGroup: /system.slice/apache2.service
             â”œâ”€11595 /usr/sbin/apache2 -k start
             â”œâ”€11598 /usr/sbin/apache2 -k start
             â”œâ”€11599 /usr/sbin/apache2 -k start
             â”œâ”€11600 /usr/sbin/apache2 -k start
             â”œâ”€11601 /usr/sbin/apache2 -k start
             â””â”€11602 /usr/sbin/apache2 -k start

Aug 24 14:53:04 pv912 systemd[1]: Starting The Apache HTTP Server...
Aug 24 14:53:05 pv912 apachectl[11584]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress t>
Aug 24 14:53:05 pv912 systemd[1]: Started The Apache HTTP Server.
```

Install additional extensions

```properties
# on droplet as silver

# Install MySQL Extension
sudo apt install php8.0-mysql

# Enable PDO module
sudo phpenmod pdo_mysql

# Other extentions
sudo apt install php8.0-common php8.0-cli php8.0-cgi php8.0-curl php8.0-gd php-imagick php8.0-mbstring php8.0-intl php8.0-pspell php8.0-imap php8.0-tidy php8.0-xmlrpc php8.0-xsl

# Install OPcache extension
sudo apt install php8.0-opcache

# Install Zip
sudo apt install php8.0-zip
```

Basic Commands
```properties
# Server Status
sudo service apache2 status
# OR
sudo systemctl status apache2

# Stop the Server
sudo service apache2 stop
# OR
sudo systemctl stop apache2

# Start the Server
sudo service apache2 start
# OR
sudo systemctl start apache2

# Re-start the Server
sudo service apache2 restart
# OR
sudo systemctl restart apache2

# Reload the Server
sudo service apache2 reload

# Enable Site
sudo a2ensite <config name>

# Disable Site
sudo a2dissite <Config Name>

# Enable Module
sudo a2enmod <module name>

# Disable Module
sudo a2dismod <module name>
```

Install MySql

```properties
# on droplet as silver

# Install MySQL Server 8
sudo apt install mysql-server

# Secure MySQL
sudo mysql_secure_installation

# Check MySQL Status
systemctl status mysql

# Output
â— mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2020-06-05 19:42:35 IST; 9min ago
   Main PID: 63433 (mysqld)
     Status: "Server is operational"
      Tasks: 40 (limit: 4624)
     Memory: 319.2M
     CGroup: /system.slice/mysql.service
             â””â”€63433 /usr/sbin/mysqld

Jun 05 19:42:35 bravo systemd[1]: Starting MySQL Community Server...
Jun 05 19:42:35 bravo systemd[1]: Started MySQL Community Server.

```

Configure MySql

```properties
# on droplet as silver

# Login to MySQL
sudo mysql

# Check password scheme of root user
SELECT user,authentication_string,plugin,host FROM mysql.user;

# Change to new and recommended password plugin - caching_sha2_password
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'root';

# Apply changes
flush privileges;

# Quit Database
exit
```

Basic Commands
```properties
# Check server status
sudo service mysql status

# Stop server
sudo service mysql stop

# Start server
sudo service mysql start

# Restart server
sudo service mysql restart
```

## Install GIT
```properties
# on droplet as silver

# Install GIT
sudo apt-get install git

# Check version
git --version
```

## Connect droplet to GitHub (Bitbucket... etc)
```properties
# on droplet as silver

# generate ssh key for GitHub connection
ssh-keygen -t rsa -b 4096 -C "github"

# add public key to GitHub

# check connection
ssh -T git@bitbucket.com
```

## Prepare directory for project
```properties
# on droplet as silver

# create directory for applications
sudo mkdir /var/www/apps

# set access to current user and apache
sudo chown $USER:www-data /var/www/apps

# make sure any files created in the folder are automatically given www-data as the group owner
sudo chmod g+s /var/www/apps
sudo chmod o-rwx /var/www/apps

# clone your repository
cd /var/www/apps
git clone git@github.com:itstep-org/itstep_pv912_php.git
```

## Laravel project Deployment 








