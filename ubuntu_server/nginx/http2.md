# Configure HTTP/2
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