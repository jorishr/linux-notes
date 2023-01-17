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