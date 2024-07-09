# Reference:

https://chatgpt.com/c/462d8ab3-0c76-42d1-9806-d1ca75a6a1f2https://docs.suitecrm.com/8.x/admin/installation-guide/downloading-installing/

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


## assign fixed ip
# ip4 is much easier, though it costs $40/year


## install apache, php, mysql

https://www.digitalocean.com/community/tutorials/how-to-install-lamp-stack-on-ubuntu

sudo apt update
sudo apt install apache2
sudo apt install openssl
sudo a2enmod ssl
sudo a2ensite default-ssl

### open http and https ports
### test apache installation
### install ssl certificates:
sudo apt update
sudo apt install certbot python3-certbot-apache
sudo certbot --apache
sudo systemctl status certbot.timer

### modify apache to automatically convert http connection to https connection
sudo a2enmod rewrite
sudo systemctl restart apache2

sudo emacs -nw /var/www/html/.htaccess
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
</IfModule>

sudo a2ensite default-ssl
sudo systemctl restart apache2

### install mysql:
sudo apt install mysql-server
sudo mysql

### download suitecrm code
https://docs.suitecrm.com/8.x/admin/releases/
wget https://github.com/salesagility/SuiteCRM-Core/releases/download/v8.6.1/SuiteCRM-8.6.1.zip





sudo apt -y install software-properties-common
sudo add-apt-repository ppa:ondrej/php
### google latest version of php; sudo apt -y install phpX.X
### follow install_php_composer

    
