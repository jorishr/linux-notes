## Optimize Nginx for performance

- [Optimize Nginx for performance](#optimize-nginx-for-performance)
  - [General optimizations](#general-optimizations)
  - [Header security](#header-security)
  - [DDOS protection](#ddos-protection)
  - [Caching policies](#caching-policies)

### General optimizations

- The number _worker processes_ can be adjusted according to the number of cpu core you have available on the server. If there is only one, no changes are need. But if you have 4 cores available (check `nproc` or ``lscpu`) configure additional worker processes in the `/etc/nginx/nginx.conf`. Set the value to `auto` to let Nginx auto-detect changes.

- The number of _worker connections_ is the highest number of connections a single worker process can handle. This number is related to the number of your CPU core's and the number of files your operating system is allowed to open per core. Check `ulimit -n`. This is also a global nginx setting that can be configured inside the events context with the following directive: `events { worker_connections 1024; }`

- _Compress responses_ by configuring the gzip settings in the http context. By default, NGINX compresses HTML responses. To compress other file formats, you'll have to pass them as parameters to the `gzip_types` directive. Configuring compression in NGINX is not enough, however. The client has to ask for the compressed response instead of the uncompressed responses. See the `add_header Vary Accept-Encoding;` in the section below on caching. The gzip compression can be very substantial so it's worth configuring this option for faster load times.
- _Caching static content_ is best done per server block because the type of content being served may be different. A regular expression can be used to target all files that end with a specific extension.

```bash
location ~* \.(css|js|jpg|webp)$ {
	#access_log off;

	#add_header Pragma public;	# legacy that does the same thing as Cache-Control
	add_header Cache-Control public max-age=31536000;
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

Particularly important is the Content Security Policy. The general config as is below is very permissive. For an up to date example and checklist see [Content Security Policy](https://csp.withgoogle.com/docs/index.html)

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

### Caching policies

See [A Guide Caching Nginx](https://www.nginx.com/blog/nginx-caching-guide/)
