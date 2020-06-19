# SSL Setup on Ubuntu Server
Table of contents
- [SSL Setup on Ubuntu Server](#ssl-setup-on-ubuntu-server)
  - [Let's Encrypt](#lets-encrypt)
    - [Install Certbot](#install-certbot)
    - [Obtain the certificate](#obtain-the-certificate)
    - [Auto-renewal](#auto-renewal)
  - [Adding a commercial certificate](#adding-a-commercial-certificate)
    - [Obtain certificate](#obtain-certificate)
    - [Setup in Nginx](#setup-in-nginx)

## Let's Encrypt
Reference guide: [Secure Nginx with Let's Encrypt](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04)

### Install Certbot
Certbot needs to be able to find the correct server block in your Nginx configuration for it to be able to automatically configure SSL. 

Specifically, it does this by looking for a `server_name` directive that matches the domain you request a certificate for.

Find the server name in your `sites-available` config file.

Update the firewall settings.
```
sudo add-apt-repository ppa:certbot/certbot

sudo apt install python-certbot-nginx
sudo systemctl reload nginx

sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```
### Obtain the certificate
Run the certbot with the `--nginx plugin`, using `-d` to specify the names we'd like the certificate to be valid for.

`sudo certbot --nginx -d example.com -d www.example.com`

### Auto-renewal
Let's Encrypt certificates are only valid for ninety days. This is to encourage users to automate their certificate renewal process. 

The certbot package we installed takes care of this for us by adding a renew script cronjob to `/etc/cron.d`. 

To test it: `sudo certbot renew --dry-run`

## Adding a commercial certificate
### Obtain certificate
Create a PRIVATE KEY through third party certificate issuing website. 

NOTE: use the full domain `www.mywebsite.com`

Create a private key file.

Create a bundle file with, in order:
- certificate
- CA
- Issuer Authority

### Setup in Nginx
In `site-available` you have the path for the bundle and the private key.
```
ssl_certificate -> /etc/ssl/private/ssl-bundle.crt;

ssl_certificate_key -> /etc/ssl/private/<name>.key;
```
Put both files in the respective location and restrict access to root.

Restart nginx: `sudo systemctl restart nginx`