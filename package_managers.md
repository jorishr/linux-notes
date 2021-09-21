# Linux Package Managers

## APT
```
apt list | less

apt search <name>
apt show <name> //-> show package detalled info

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
## SNAPD
