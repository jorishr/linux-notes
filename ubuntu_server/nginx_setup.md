# Nginx Setup
Table of contents
- [Nginx Setup](#nginx-setup)
	- [Basic install](#basic-install)
		- [Firewall setup](#firewall-setup)
		- [Nginx status check](#nginx-status-check)
		- [Check configuration for syntax errors](#check-configuration-for-syntax-errors)
		- [Autoboot Nginx](#autoboot-nginx)
	- [Server Blocks](#server-blocks)
		- [Default server block](#default-server-block)
		- [Creating new server blocks](#creating-new-server-blocks)
			- [Config file](#config-file)
			- [Enable configuration](#enable-configuration)
			- [Hash bucket memory problem](#hash-bucket-memory-problem)
	- [Nginx Structure](#nginx-structure)
		- [Nginx basic configuration](#nginx-basic-configuration)
	- [Nginx server logs](#nginx-server-logs)
	- [Client IP in Nginx](#client-ip-in-nginx)
		- [Host](#host)
		- [X-REAL-IP/X-FORWARDED-FOR](#x-real-ipx-forwarded-for)

Reference guide: [Nginx on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)

## Basic install
```
sudo apt update
sudo apt install nginx
```
### Firewall setup
Nginx registers itself as a service with the `ufw` upon installation, making it straightforward to allow Nginx access, `sudo ufw app list`.

Nginx Full: This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic), `sudo ufw allow 'Nginx <option>'`

### Nginx status check

`systemctl status nginx`

You can access the default Nginx landing page to confirm that the software is running properly by navigating to your server's IP address. 
```
sudo systemctl stop nginx
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
```
If you are simply making configuration changes, Nginx can often reload without dropping connections.

### Check configuration for syntax errors
To check errors in the config files: `sudo nginx -t`.

### Autoboot Nginx
By default, Nginx is configured to start automatically when the server boots. 
```
sudo systemctl enable nginx
sudo systemctl disable nginx
```

## Server Blocks
When using the Nginx web server, server blocks (similar to virtual hosts in Apache) can be used to encapsulate configuration details and host more than one domain from a single server.

### Default server block
Configured to serve documents out of a directory at `/var/www/html`.

### Creating new server blocks

`sudo mkdir -p /var/www/example.com/`

Assign OWNERSHIP of the directory with the $USER environment variable:

`sudo chown -R $USER:$USER /var/www/example.com/`

#### Config file
`sudo nano /etc/nginx/sites-available/example.com`

The content has the server directives:
```
server {
	listen <port>;
	root /var/www/example.com;
	index index.html index.htm index.nginx-debian.html;
	
      server_name example.com www.example.com;

      location / {
 	         try_files $uri $uri/ =404;
      }
}
```
#### Enable configuration
Enable the config file by creating a link from it to the `sites-enabled` directory, which Nginx reads from during startup:

`sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/`

#### Hash bucket memory problem
To avoid a possible hash bucket memory problem that can arise from adding additional server names, it is necessary to adjust a single value in the `/etc/nginx/nginx.conf` file. 

Open the file:
`sudo nano /etc/nginx/nginx.conf`

Remove the `#` before the line: `server_names_hash_bucket_size 64;`


## Nginx Structure
### Nginx basic configuration
The Nginx configuration directory is: `/etc/nginx`. All of the Nginx configuration files reside here.

The main Nginx configuration file is: /`etc/nginx/nginx.conf`. This file can be modified to make changes to the Nginx global configuration.

The directory where per-site server blocks can be stored is `/etc/nginx/sites-available/`. Nginx will not use the configuration files found in this directory unless they are linked to the `sites-enabled` directory. 

## Nginx server logs
Every request to your web server is recorded in a log file unless Nginx is configured to do otherwise. See `/var/log/nginx/access.log`.

Any Nginx errors will be recorded in the error log: `/var/log/nginx/error.log`.

## Client IP in Nginx
Reference video: [Client IP in Nginx](https://www.youtube.com/watch?v=4p1Zc8F29Lk&t=210s)

### Host
The most important is HOST. A single server can host multiple websites. To direct the user to the correct one the HTTP REQUEST contains a HEADER with a HOST. It is the addresss in the address bar that gets encoded into the HOST HEADER.

In the LOCATION block of NGINX you will find the `proxy_set_header Host $host`;

### X-REAL-IP/X-FORWARDED-FOR
These headers are not important for security reasons because they can easily be faked. They can however help you setup logs and analytics.

To set it up in the LOCATION block of the server:
```
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```
The forwarded-for will contain all the possible intermediate ip addresses of the proxy servers that the requests passed through.