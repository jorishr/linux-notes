# Advaced Package Tools (APT)
- APT is a built-in package installer for linux. 
```
sudo apt-get update         //-> update package listings

apt list --upgradable       //-> see what newer versions

sudo apt-get upgrade        //-> install new versions

apt-cache search --names-only ^php
//-> search for available php packages and modules

apt-cache policy <name>
//-> check installed version of an application

which <program>
//-> find the path where the program is installed
```
- PPA is the personal package archive which can hold the programs that are not available through the main repositories.

- SNAPD is an app store for linux:
```
snap install <program>
```

- NPM is the nodejs package manager

- TASKSEL is a script that simplifies the install of packages that are often combined. For example, a LAMP stack or mail server.

Does not come standard on every Linux version but can be installed via: `sudo apt install tasksel`