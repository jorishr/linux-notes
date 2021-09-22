# Linux Package Managers

## Advanced Packagae Tools (APT)
- List and search apps
```
apt list | less

apt search <name>

apt-cache search --names-only ^php
//-> search for available php packages and modules

apt-cache policy <name>
//-> check installed version of an application

apt show <name> //-> show package detalled info

which <program>
//-> find the path where the program is installed
```
- upgrade and remove packages
```
sudo apt update //-> update the local package list repository

cat /etc/apt/sources.list   //-> show the repositories the system is aware of

sudo apt autoremove //-> remove unused packages from older kernel versions

sudo apt list --upgradable
sudo apt upgrade

#LTS upgrades: use tmux!
do-release-upgrade 

#Automate minor updates and security fixes
sudo dpkg-reconfigure unattended-upgrades
```
## SNAP
Snap is a platform that allows for the development of distribution independent packages. A snap is a self-contained loop file system. This runs on top a standardized foundation of software that provides the interaction with the linux system. See [snapcraft.io](https://snapcraft.io).

Snap software updates automatically on set time intervals.

```
snap find <name>
snap install <name>

ls /snap/       //-> inspect directory with installed snaps

sudo snap refresh   //-> check for updates
sudo snap remove <name>
```
## Personal Package Archive (PPA)
PPA is the personal package archive which can hold the programs that are not available through the main repositories.

## Tasksel
Tasksel is a script that simplifies the install of packages that are often combined. For example, a LAMP stack or mail server. Does not come standard on every Linux version but can be installed via: `sudo apt install tasksel`