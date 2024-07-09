# Reference:

https://chatgpt.com/c/462d8ab3-0c76-42d1-9806-d1ca75a6a1f2
https://docs.suitecrm.com/8.x/admin/installation-guide/downloading-installing/

# Step by step:

## create a t3-small instance on aws with ubuntu

## log into new instance

## setup ssh and clone source
ssh-keygen
cat ~/.ssh/*pub
git clone git@github.com:crkrenn/SuiteCRM.git

## install emacs
sudo apt update
sudo apt  install emacs-nox

## open this document
cd SuiteCRM/
emacs -nw README_install.md

## install ubuntu pro
https://ubuntu.com/pro
https://ubuntu.com/pro/dashboard

sudo apt update && sudo apt upgrade
pro --version
pro security-status
### copy paste "sudo pro attach" command from https://ubuntu.com/pro/dashboard


## assign fixed ip
# ip4 is much easier, though it costs $40/year


## install apache

https://www.digitalocean.com/community/tutorials/how-to-install-lamp-stack-on-ubuntu

sudo apt update
sudo apt install apache2
sudo apt install openssl
sudo a2enmod ssl
sudo a2ensite default-ssl

### see apache_two_sites.md (need to edit http conf and https/ssl conf)

### open http and https ports
### test apache installation
### install ssl certificates:
sudo apt update
sudo apt install certbot python3-certbot-apache
sudo certbot --apache

sudo systemctl status certbot.timer


### install mysql:
sudo apt install mysql-server
sudo mysql

<!-- mysql -u root -p -->

CREATE DATABASE suitecrm_db;

CREATE USER 'suitecrmuser'@'localhost' IDENTIFIED BY 'strongpassword';
GRANT ALL PRIVILEGES ON suitecrm_db.* TO 'suitecrmuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

#### default port: 3306

### install php
sudo apt -y install software-properties-common
sudo add-apt-repository ppa:ondrej/php

### install appropriate version of php sudo apt -y install php8.2

### install needed packages:

#### required php packages (https://docs.suitecrm.com/8.x/admin/installation-guide/webserver-setup-guide/)
cli
curl
common
intl
json
gd
mbstring
mysqli
pdo_mysql
openssl
soap
xml
zip
imap (optional)
ldap (optional)

Note: pdo_mysql and openssl are installed even with the following errors:
E: Unable to locate package php-pdo_mysql
E: Unable to locate package php-openssl


php -m | grep -e curl

sudo apt-get update

sudo apt install php8.2-ldap php8.2-curl php8.2-intl php8.2-json php8.2-gd php8.2-mbstring php8.2-mysqli php8.2-pdo_mysql php8.2-openssl php8.2-soap php8.2-xml php8.2-zip php8.2-imap php8.2-intl  php8.2-gd php8.2-soap php8.2-mbstring php8.2-xml php8.2-zip php8.2-imap php8.2-ldap


sudo apt install php-ldap php-curl php-intl php-json php-gd php-mbstring php-mysqli php-pdo_mysql php-openssl php-soap php-xml php-zip php-imap

sudo systemctl restart apache2


/etc/php/8.2/cli/php.ini
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE & ~E_WARNING




### download suitecrm code
sudo apt install unzip
https://docs.suitecrm.com/8.x/admin/releases/
wget https://github.com/salesagility/SuiteCRM-Core/releases/download/v8.6.1/SuiteCRM-8.6.1.zip


sudo find . -type d -not -perm 2755 -exec chmod 2755 {} \;
sudo find . -type f -not -perm 0644 -exec chmod 0644 {} \;
sudo find . ! -user www-data -exec chown www-data:www-data {} \;
sudo chmod +x bin/console

cd /etc/apache2/sites-enabled
sudo emacs crm.*
<VirtualHost *:80>

    DocumentRoot /<path-to-suite>/public
    <Directory /<path-to-suite>/public>
        AllowOverride All
        Order Allow,Deny
        Allow from All
    </Directory>

</VirtualHost>

visit crm.democracygps.org and follow installation instructions

# configure updates (system and suitecrm) and backups



## install google email (see install_google_email.md in this directory)
### get new app password from https://myaccount.google.com/security
sudo apt update
sudo apt install msmtp msmtp-mta
### edit  ~/.msmtprc
### test echo -e "Subject: Test Email\n\nThis is a test email from msmtp." | msmtp <email_address>

## enable unattended updates and email notifications: (unattended_updates.md)
sudo apt update
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
sudo nano /etc/apt/apt.conf.d/10periodic

### follow instructions for msmtp mail
### test with `sudo unattended-upgrade -d`
