# Setting up a local development environment in Linux
- [Setting up a local development environment in Linux](#setting-up-a-local-development-environment-in-linux)
  - [General tools](#general-tools)
  - [LAMP stack](#lamp-stack)
  - [MERN Stack](#mern-stack)

## General tools
- Terminator cli
- Studio Visual code
- Filezilla

## LAMP stack
1. Apache server
- install
`sudo apt-get install apache2`
- webroot location: `var/www/html`
- test with an index.html file in browser: `localhost/index.html`

2. PHP

Latest stable version: `sudo apt-get install php`

Switch between PHP versions:
```
sudo ln -sfn /usr/bin/phpX.X /etc/alternatives/php	
//-> check dir for exact version

sudo a2dismod phpX.X ; 
sudo a2enmod phpX.X ; 

systemctl restart apache2
```
[Reference manual](https://connectwww.com/how-to-install-and-configure-apachephpmysql-and-phpmyadmin-on-ubuntu/727/)


3. MySql

`apt-get install mysql-server`

See mysql doc for further instruction on setup.
Use phpMyAdmin for easy database management.

## MERN Stack