# Ubuntu server setup
Table of contents
- [Ubuntu server setup](#ubuntu-server-setup)
  - [Access and firewall](#access-and-firewall)
    - [Add user](#add-user)
    - [UFW Firewall](#ufw-firewall)
    - [Public key](#public-key)
  - [Fail2ban](#fail2ban)
  - [Additional security measure to consider](#additional-security-measure-to-consider)
  
Create a new VPS on a cloud provider platform.

## Access and firewall
Enable the root user login via ssh keys or password through the VPS provider.

### Add user
Add new user and set sudo privileges:
```
adduser <user>

usermod -aG sudo <user>	
//-> -a for append, -G for list of groups, adds the user to the group "sudo" which is configured for admin privileges
```
### UFW Firewall
Once enabled the firewall will block access to all services, unless specified otherwise. 

Allow OpenSSH access.
```
ufw app list
ufw allow OpenSSH
ufw enable
ufw status
```
Reference guide: [UFW common firewall rules](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

### Public key
Grant user access via public key. The public key is already in the root account's `~/.ssh/authorized_keys` file on the server. We can copy that file and directory structure to our new user account in our existing session:

`rsync --archive --chown=<user>:<user> ~/.ssh /home/<user>`

## Fail2ban
Install the fail2ban app to block IP's with suspicious activity. The program will load automatically as a systemd service after install. `sudo apt install fail2ban` 

## Additional security measure to consider
- SSH: upgrade from protocol 1 to protocol 2
- SSH: change default port from 22 to something less obvious
- Think carefully about User permissions and User Groups. Who needs access to what and which programs need to root privileges, which one's don't?