# Secure File Transfer
## SFTP

The FTP protocol uses port 21 and transfers files as packets of text that can be read.

SFTP is the secure file transfer protocol, with secure login as in ssh:
```
sftp <user>@<serveraddress>
ls		//-> list of files and dirs on remote server
lls		//-> list of files in the local dir

put sample.txt		//-> transfer TO server
get sample.txt		//-> transfer FROM server to local machine
```
## Secure Copy

Copy a file from the local command line to a folder on the remote server:
```
scp <localFile> <user>@<serverIP>:~/serverFolder
```
## RSYNC

Synchronize local and remote folder
rsync -av . <usr>@<domain>:~/<serverDestFolder>

Delete a file and run rsync again, and it only copies the one mising file.

[SSH Cheat Sheet](https://gist.github.com/bradtraversy/f03df587f2323b50beb4250520089a9e)