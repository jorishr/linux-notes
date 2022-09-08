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
	- [Optimize Nginx for performance](#optimize-nginx-for-performance)
		- [Header security](#header-security)
		- [DDOS protection](#ddos-protection)
	- [PHP and Nginx](#php-and-nginx)
	- [Nginx as a load balancer](#nginx-as-a-load-balancer)
	- [Configure SSL and HTTP/2](#configure-ssl-and-http2)
		- [HTTP/2 Server Push](#http2-server-push)

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
	proxy_http_version 1.1; 
	# default is 1.0; websocket requires 1.1; no need/support for http2
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

## Optimize Nginx for performance
- The number *worker processes* can be adjusted according to the number of cpu core you have available on the server. If there is only one, no changes are need. But if you have 4 cores available (check `nproc` or ``lscpu`) configure additional worker processes in the `/etc/nginx/nginx.conf`. Set the value to `auto` to let Nginx auto-detect changes.

- The number of *worker connections* is the highest number of connections a single worker process can handle. This number is related to the number of your CPU core's and the number of files your operating system is allowed to open per core. Check `ulimit -n`. This is also a global nginx setting that can be configured inside the events context with the following directive: `events { worker_connections 1024; }`

- *Compress responses* by configuring the gzip settings in the http context. By default, NGINX compresses HTML responses. To compress other file formats, you'll have to pass them as parameters to the `gzip_types` directive. Configuring compression in NGINX is not enough, however. The client has to ask for the compressed response instead of the uncompressed responses. See the `add_header Vary Accept-Encoding;` in the section below on caching. The gzip compression can be very substantial so it's worth configuring this option for faster load times. 
   
- *Caching static content* is best done per server block because the type of content being served may be different. A regular expression can be used to target all files that end with a specific extension.
```bash
location ~* \.(css|js|jpg|webp)$ {
	#access_log off;
	
	#add_header Pragma public;	# legacy that does the same thing as Cache-Control
	add_header Cache-Control public;	
	# tells the client that this content can be cached in any way
	add_header Vary Accept-Encoding;	
	# tells the client (browser) to expect different (sized) responses based on the type of request (e.g. compressed or not)
	expires 1M; # clear cache every month; 24h (hours) or 10m (minutes) 
```
```bash
	gzip on;

	gzip_vary on;
	gzip_proxied any;
	gzip_disable "msie6";
	gzip_comp_level 4;
	gzip_buffers 32 8k;
	gzip_min_length 250;
	gzip_http_version 1.1;
	gzip_types image/jpeg image/bmp image/svg+xml text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript image/x-icon;
```
### Header security
These are basic protections against the most common attacks like click-jacking or cors attacks. See [Nginx Improvements](https://www.freecodecamp.org/news/powerful-ways-to-supercharge-your-nginx-server-and-improve-its-performance-a8afdbfde64d/)
```bash
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header X-Content-Type-Options "nosniff" always;
add_header Referrer-Policy "no-referrer-when-downgrade" always;
add_header Content-Security-Policy "default-src * data: 'unsafe-eval' 'unsafe-inline'" always;
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
```
### DDOS protection
DDOS protection requires a layered approach and you're not going to fight off an attack by tweaking the configuration files. However, you can implement some minimal protection via rate and connection limits or by dropping slow connections.

See [Mitigating DDOS](https://www.nginx.com/blog/mitigating-ddos-attacks-with-nginx-and-nginx-plus/)

## PHP and Nginx
See [PHP With NGINX](https://www.freecodecamp.org/news/the-nginx-handbook/#php-with-nginx))

## Nginx as a load balancer
Load balancing is a much broader topic and usually involves multiple servers. However, if you run various NodeJS applications on the same server Nginx may be configured to distribute the load between them automatically. To do so you can add an *upstream* context block to the server configuration:
```bash
http {

    upstream backend_servers {
        server localhost:3001;	# nodejs 1
        server localhost:3002;	# nodejs 2
        server localhost:3003;	# nodejs 3
    }

    server {

        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend_servers;
			# all traffic is passed on to the backend_servers upstream
        }
    }
}
```
## Configure SSL and HTTP/2
For the HTTP/2 to work correctly you need a SSL certificate first.

The http/2 package is usually included in the default package on the Ubuntu repo. Simply update the server block settings managed by Certbot:
```bash
    listen [::]:443 ssl http2 ipv6only=on; 
    listen 443 ssl http2; 
``` 
Note: When upgrading from HTTP 1.1 on an existing Certbot generated certificate, problems may arise with the encryption ciphers. See [Nginx with HTTP/2 setup](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-with-http-2-support-on-ubuntu-18-04)

Check the configuration: `curl -I -L https://your_domain`

SSL/TLS is a broad topic. But the standard Nginx settings are not good enough. TLSv1.3 and TLSv1.2 are the current standard (2022) and everything below should only be considered because of legacy support. To obtain the an A or A+ score via benchmark tools consider the following optimizations in the `nginx.conf`:
```bash
##
# SSL Settings
##

ssl_protocols TLSv1.3 TLSv1.2;

ssl_prefer_server_ciphers on;
ssl_dhparam dhparam.pem;				# generate on server
ssl_ciphers EECDH+AESGCM:EDH+AESGCM;	# cipher is always changing; look up the latest

ssl_ecdh_curve secp384r1;

ssl_stapling on;
ssl_stapling_verify on;

ssl_session_timeout  10m;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
```
The key-exchange key should be generated on the server and might take a while: `openssl dhparam -out /etc/nginx/ssl/dhparam.pem 4096`.

*SSL Stapling* doesn't make the server more secure, but it does help the client by telling them they can use your server for OCSP information for your domain instead of letting the browser make the request to an often unreliable resource.

References:
- [Cipher list](https://cipherlist.eu/)
- [Nginx Admin's Handbook](https://github.com/trimstray/nginx-admins-handbook#prologue)
### HTTP/2 Server Push
HTTP/2 unlocks additional configuration options like *server push* that can further reduce loading times. See [Nginx Server Push](https://www.nginx.com/blog/nginx-1-13-9-http2-server-push/)