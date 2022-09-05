# Nginx Setup
Table of contents
- [Nginx Setup](#nginx-setup)
	- [Basic install](#basic-install)
		- [Firewall setup](#firewall-setup)
		- [Nginx status check](#nginx-status-check)
		- [Check configuration for syntax errors](#check-configuration-for-syntax-errors)
		- [Autoboot Nginx](#autoboot-nginx)
	- [Configuration files](#configuration-files)
		- [Dynamic routing](#dynamic-routing)
	- [Nginx server logs](#nginx-server-logs)
	- [Nginx Reverse Proxy](#nginx-reverse-proxy)
		- [NodeJS app](#nodejs-app)
		- [X-REAL-IP/X-FORWARDED-FOR](#x-real-ipx-forwarded-for)
	- [Configure SSL and HTTP/2](#configure-ssl-and-http2)
	- [Optimize Nginx for performance](#optimize-nginx-for-performance)
	- [PHP and Nginx](#php-and-nginx)
	- [Nginx as a load balancer](#nginx-as-a-load-balancer)

References: 
- [Install Nginx on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)
- [The Nginx Handbook](https://www.freecodecamp.org/news/the-nginx-handbook)
- [Nginx documentation](https://nginx.org/en/docs/)
- [Apache vs Nginx, practical considerations](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations)
- [Config assistant Nginx](https://www.digitalocean.com/community/tools/nginx)

## Basic install
```
sudo apt update
sudo apt install nginx
```
### Firewall setup
Nginx registers itself as a service with the `ufw` upon installation, making it straightforward to allow Nginx access, `sudo ufw app list`.

Nginx Full: This profile opens both port 80 (normal, un-encrypted web traffic) and port 443 (TLS/SSL encrypted traffic), `sudo ufw allow 'Nginx <option>'`

### Nginx status check

`systemctl status nginx`

You can access the default Nginx landing page to confirm that the software is running properly by navigating to your server's IP address. 
```bash
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
```bash
sudo systemctl enable nginx
sudo systemctl disable nginx
```

## Configuration files
The main config file is located at `/etc/nginx/nginx.conf`. 

The *main* context is the configuration file itself. The *event* context handles global configuration settings. The *http* context handles all directive related to http and https requests. There can be only one event and one http context in a configuration file. The *server* context is nested inside the http context and is used for configuring one specific virtual server. There can be multiple virtual server configurations in one config file.

The *listen* directive is used to determine the correct server context in case of multiple server blocks. Thus app1 could be running on port 3000 and app2 on port 3001. Another way to differentiate is by using the `$host` variable or the `server_name` directive. Thus http web traffic coming in via port 80 can be re-directed based on the domain name from which the traffic was originated.  
```bash
# nginx.conf
event {}
http {
    include /etc/nginx/mime.types;
	# By itself nginx is dumb when it comes to file types.
	# You could include file type context manually:
	# types {text/html html; text/css css;}

	# To avoid a possible hash bucket memory problem that can arise from adding additional server names:
	server_names_hash_bucket_size 64;
	
	server {}
	server {
		listen 80;
		server_name <name>;
	}
}
mail {}
```
In practice the global configuration file includes a directive inside the *http context* to include the configuration files related to specific domains or apps that run on the server, located in `/etc/nginx/sites-enabled`.

Example configuration for a partial config file for 'example.com' `sudo nano /etc/nginx/sites-available/example.com`. Enable the config file by creating a link from it to the `sites-enabled` directory, which Nginx reads from during startup:

`sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/`

The content has the server *directives*. In the example below 'server' is a *block directive* that can have multiple *simple directives* inside the curly braces, often called a context:
```bash
server {
	listen 80;
	root /var/www/example.com;
	index index.html index.htm index.nginx-debian.html;
	
	server_name example.com www.example.com;
	error_page 404 /error.html;
	# return 404 "Error 404: page not found";

	location / {
			try_files $uri $uri/ =404;
			#first the uri from the request is attempted; next the root folder; if no luck a 404 error is thrown, in this case handled by the error_page directive.
	}
}
```
You can serve data from any directory on the file system. But the root folder `/srv` is usually reserved for this purpose. Alternatively the `/var/www/html` is also used. 

### Dynamic routing
The *location* context can change the files that are being server based on the URI used in the request to the server. Thus is the default is to server an `/srv/index.html`, you could use `location /hello { return 200 "Hello"; }` to return a string 'hello' when visiting the url 'domain/hello'. By default your modifier is treated as a prefix. Thus `/hello` will also match requests to 'hello1' or 'hello-world'. For an exact match use `location = /hello`. Regular expressions can also be used, see [dynamic routing](https://www.freecodecamp.org/news/the-nginx-handbook/#dynamic-routing-in-nginx).

The location context can also be used to explicitly redirect to different pages. `location = /about { return 307 /about.html; }` Similarly you can use a rewrite, which re-evaluates the server context. This computationally more expensive but does not notify the end user explicitly about a redirect. A rewrite happens *outside* the location context `rewrite /about_page /about.html`. 

*Variables* can be declared anywhere inside a config file using `set <var_name> <value>`. The value can be a Boolean, a string or a number. Some variables are baked into Nginx. Thus for a request to /example.com/hello?name=john you have access to: $host: the root (example.com); $uri: the requested uri (/hello); $args: all the query strings (name=john). 

## Nginx server logs
Every request to your web server is recorded in a log file unless Nginx is configured to do otherwise. See `/var/log/nginx/access.log`.

Any Nginx errors will be recorded in the error log: `/var/log/nginx/error.log`.

## Nginx Reverse Proxy
When configured as a reverse proxy, NGINX sits between the client and a back end server (NodeJS for example). The client sends requests to NGINX, then NGINX passes the request to the back end. Once the back end server finishes processing the request, it sends it back to NGINX. In turn, NGINX returns the response to the client. The end user doesn't see that this process is happening. 

In the config file you can recognize the the *proxy* directives inside the location context block. You could simple serve a different website: `location / { proxy_pass "https://nginx.org/"; }`.

### NodeJS app
The NodeJS server is usually running on a port of the localHost. Thus the idea is to redirect traffic to that port on the localhost: `location / { proxy_pass http://localhost:3000; }`. To handle websocket connections, additional configuration is required. Moreover, by default NodeJS will not see all the data that comes with the http request. You have to tell Nginx to pass headers or client ip's. for example.
```bash
location / {
	proxy_pass http://localhost:3000;
	proxy_http_version 1.1; # default is 1.0; websocket requires 1.1
	proxy_set_header Host $host
	proxy_set_header Upgrade $http_upgrade; 
	proxy_set_header Connection 'upgrade';
	# setting a header on the back-end server.
}
```
### X-REAL-IP/X-FORWARDED-FOR
These headers are not important for security reasons because they can easily be faked. They can however help you setup logs and analytics.
```bash
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```
The forwarded-for will contain all the possible intermediate ip addresses of the proxy servers that the requests passed through.

## Configure SSL and HTTP/2

## Optimize Nginx for performance

## PHP and Nginx
See [PHP With NGINX](https://www.freecodecamp.org/news/the-nginx-handbook/#php-with-nginx))

## Nginx as a load balancer