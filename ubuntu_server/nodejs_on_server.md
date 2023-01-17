# NodeJs Setup on server
- [NodeJs Setup on server](#nodejs-setup-on-server)
  - [Install](#install)
  - [App Setup](#app-setup)
  - [Process Manager 2 (PM2)](#process-manager-2-pm2)
  - [Nginx as a reverse proxy](#nginx-as-a-reverse-proxy)
  - [Environment Variables](#environment-variables)
  - [References:](#references)

## Install
First, install the NodeSource PPA in order to get access to its contents. Make sure you're in your home directory, and use `curl` to retrieve the installation script for the Node.js 8.x archives:
```bash
cd ~
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh

sudo bash nodesource_setup.sh
sudo apt install nodejs
nodejs -v
```
In order for some npm packages to work (those that require compiling code from source, for example), you will need to install the build-essential package: `sudo apt install build-essential`

## App Setup
Copy files to the server and install your packages first with `package.json`
`npm install --production`. The exact build process may differ from app to app.

## Process Manager 2 (PM2)
PM2 is a process manager for NodeJs applications. PM2 makes it possible to daemonize applications so that they will run in the background as a service.

`sudo npm install pm2@latest -g`

PM2 automatically assigns an app name (based on the filename, without the .js extension) and a PM2 id. PM2 also maintains other information, such as the PID of the process, its current status, and memory usage. PM2 can also act as a load balancer.
```bash
pm2 start <app>.js --name=myApp
pm2 status

pm2 list
pm2 info <app>
pm2 show <PID>
pm2 monit

pm2 restart <process-name>
```
Applications that are running under PM2 will be restarted automatically if the application crashes or is killed.

To enable load on system startup: `pm2 startup systemd`

Run the command from the output, with your username in place of SAMMY:
```bash
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u sammy --hp /home/sammy

pm2 save
```
As an additional step, we can save the PM2 process list and corresponding environments. Now you have created a systemd unit that runs pm2 for your user on boot. This pm2 instance, in turn, runs `<app>.js`.

## Nginx as a reverse proxy
Once the app is running and listening on localhost, you need to set up a way for your users to access it. Set up the Nginx web server as a reverse proxy for this purpose.

Open the config file of the website: `sudo nano /etc/nginx/sites-available/example.com`
```bash
location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```
This configures the server to respond to requests at its root. Assuming our server is available at example.com, accessing https://example.com/ via a web browser would send the request to hello.js, listening on port 3000 at localhost.

You can add additional location blocks to the same server block to provide access to other applications on the same server. 

For example, if you were also running another Node.js application on port 3001, you could add this location block to allow access to it via https://example.com/app2:
```
location /app2 {
    proxy_pass http://localhost:3001;
    ...
}
```
## Environment Variables
When running multiple apps in one NodeJs environment there is an issue with the .env files. Basically all apps are running in the same NodeJs environment and globally there can be only one .env file in the `HOME` directory. You can keep additional .env files in the root data folder of each app, for example, `/var/www/myapp`, but make sure the permissions are setup correctly and that this file cannot be accessed.

## References: 
- [Setup NodeJs app for production on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-18-04)
- [Express_NodeJs Deployment](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/deployment)