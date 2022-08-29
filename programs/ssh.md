# Secure Shell Host (SSH)
Table of contents
- [Secure Shell Host (SSH)](#secure-shell-host-ssh)
  - [1. About SSH](#1-about-ssh)
  - [2. Authentication methods](#2-authentication-methods)
  - [3. Github configuration](#3-github-configuration)
  - [4. Server SSH setup](#4-server-ssh-setup)
***

## 1. About SSH
Secure Shell: cryptographic network protocol that allows for secure services over insecure networks between client and server.

OpenSSH is the default windows SSH client.

To access a remote computer it needs to be running a SSHD service, or SSH Daemon. A daemon is computer program that runs a background process.

SSH breaks the data into packages of which the payload or data and padding is encrypted. The only thing visible is the package length and auth info. 

## 2. Authentication methods
- password (default): 
`ssh <username>@<ipaddress_server>`

- public/private key pair
```
ssh-keygen
//-> ~/.ssh/id_rsa_<id>		(private key)
//-> ~/.ssh/id_rsa_<id>.pub	(public key)

cat ~/.ssh/id_rsa_xxx		//-> read out key in terminal
cat id_rsa.pub | pbcopy		//-> idem + copy to clipboard
```
The public key is put onto the server in the folder ~/.ssh/authorized_keys

NOTES
- Every key that is added is linked to the specific user. Thus if you start out in setup with root user, you will have to repeat the process of adding the public key to other users you create.
- knownhosts: a configfile that contains the ip's or mac addresses of known hosts that are allowed to login
- On remote servers or windows you may have to activate the authentication client to manage passwords attached to ssh keys:
```
eval `ssh-agent -s`
ssh-add <keyName>
```
- set configuration options
```
sudo nano /etc/ssh/sshd.config

//for example to allow or deny password login via ssh
```
## 3. Github configuration
- Locally generate ssh keys with ssh-keygen and add identity to ssh-agent (windows GitBash)
```
ssh-keygen
ssh-add ~/.ssh/id_rsa_xxx
```

Next, add the public key to your github account.

- inspect repos from the command line:
```
curl -i -H 'Authorization: token <token>' https://api.github.com/user/repos
```
- create a new remote repo
```
curl -i -H 'Authorization: token <token>' -d '{"name":"<repo_name>","auto_init":"true", "gitignore_template":"Node","private":"true"}' https://api.github.com/user/repos
```
- delete remote repo
```
curl -X DELETE -H 'Authorization: token <token>' https://api.github.com/repos/{username}/{repo}
```

NOTE: See local bash scripts that have been configured to make this easier.


## 4. Server SSH setup
- install ssh
- Create a new sudo user and disable root login
```
ssh root@<ip>
adduser <username>				//-> add new ssh user
usermod -aG sudo <username>		//-> append user to sudo group
id <username> 					//-> check user info

sudo nano /etc/ssh/sshd_config
//-> set PermitRootLogin: no
//-> set PasswordAuthentication no

sudo systemctl reload sshd
```
- Add your public key to user:
```
mkdir .ssh 
touch authorized_keys
nano authorized_keys
//-> copy locally generated public key from user
```