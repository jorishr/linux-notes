# Users, Groups and Permissions
Table of contents
- [Users, Groups and Permissions](#users-groups-and-permissions)
  - [1. List, create and delete users](#1-list-create-and-delete-users)
  - [2. File and directory permissions](#2-file-and-directory-permissions)
  - [3. File Attributes](#3-file-attributes)

## 1. List, create and delete users
* List logged-in users and get current user id
`users`
`id`

* Add or delete a new user
```bash
adduser <username> 	# -> follow the steps
deluser <username>
```
* Switch to other user: `su <otheruser>`

* Add to or remove from group
```bash
usermod -aG <group> <user>	
usermod -aG sudo jorishr	
# -> appends user to group sudo

deluser <user> <group>
# -> need to login again to see effect
```

## 2. File and directory permissions
* See file/dir permissions: `ls -l`
```bash
#TYPE 	OWNER(u)	GROUP(g)	OTHERS/PUBLIC(o)
# - rwx		rw		x
# d	rwx		rw		x

# - for files
# d for directory
# p for named pipes
```
Each group has three possible characters:
r: read; w: write; x:execute

Files
- r: read file content
- w: modify file (does not prevent delete)
- x: run as executable

Directory
- r: list files (ls)
- w: add/delete files (thus even without write permission on file you could delete it if you have directory write permission)
- x: cd into the directory and access the files

To change permission:
```bash
chmod <u/g/o/a>+r/w/x <file>		
# -> add permission to file

chmod <u/g/o/a>-r/w/x <file>		
# -> remove file permissions

chmod a+r file.txt		
# -> add read permission for all users
```

Shortcut: use the number codes for each u/g/o

0 - no permission
1 - execute
2 - write
4 - read
```bash
chmod 750 <file>
# -> user: 7 = 4 + 2 + 1
# -> group: 5 = 4 + 1
# -> other: 0, access denied
```
General guidelines for folders: 755; files: 644;

NOTE: the 7,6,5,4,3,2,1 are converted into binary whereby:
```bash
# r	w	x
# 4	2	1	-> base 2
# 1	1	1	-> 7
# 1	1	0	-> 6
# 1	0	1	-> 5
# 1	0	0	-> 4
# 0	1	1	-> 3
# 0	1	0	-> 2
# 0	0	1	-> 1
```
This is repeated for each group (owner, group, others).	
	
ROOT has full permissions on all files and directories regardless of the user/group/other settings.

* Change owner of file or directory

`chown <newOwner> <file/folder>`

* Change group of file or directory

`chgrp <newGroup> <file/folder>`

`groups 	//-> see all existing groups`

## 3. File Attributes
See/change file attributes: 
```bash
lsattr -l
chattrr +a/i/s
chattrr -a/i/s
```
a: append only, you can only add content to the file

i: immutable, no modification/delete possible

s: zero out bit blocks upon delete instead of simply removing the reference