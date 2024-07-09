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


## install apache, php, mysql
sudo apt -y install software-properties-common
sudo add-apt-repository ppa:ondrej/php

