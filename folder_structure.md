# Linux Filesystem Folder Structure
Table of contents
- [Linux Filesystem Folder Structure](#linux-filesystem-folder-structure)
  - [/home](#home)
  - [/bin](#bin)
  - [/dev](#dev)
  - [/etc](#etc)
  - [/lib](#lib)
  - [/media and /mnt](#media-and-mnt)
  - [/opt](#opt)
  - [/proc](#proc)
  - [/root](#root)
  - [/run](#run)
  - [/snap](#snap)
  - [/srv](#srv)
  - [/sys](#sys)
  - [/tmp](#tmp)
  - [/usr](#usr)
  - [/var](#var)

## /home
Hidden folders in the /home folder is where you can find configuration files for some applications and cache or desktop settings.

## /bin
Contains the most basic binaries for basic functionality like the cat command to display the output of a file.

The /sbin folder contains the system binaries that a normal user doesn't have access to, only the administrator.

## /dev
Contains the devices of the computer. Every hardware component is represented by a folder and files in Unix systems.

## /etc
Stands for etcetera and contains all system-wide configurations. For example the apt repository stores the list of sources in this folder.

## /lib
The /lib folders /lib32 and /lib64 contain the libraries that application can use to perform various functions. They are primarily used by the binaries in the /bin or /sbin folders.

## /media and /mnt
Contain the external drives you plugin to your pc, like usb sticks or floppy drives.

## /opt
Stands for optional where manually installed vendor software gets installed, for example a vpn software or your own custom apps.

## /proc
Contains the pseudo files for processes running on the system.  You can look up the process id in the process manager and located the subfolder in proc/idnumber/

These are not actual files but the kernel that uses the processes that are represented by files.

You can find for example info on the cpu by reading out the content of these files:
`cat proc/cpuinfo`

## /root
Root user home folder.

## /run
Runs in the RAM as tempfs or temporary file system which gets deleted on reboot.

## /snap
Contain the snap packages that are installed. Snap packages are self-contained programs.

## /srv
Server data if you run a http server.

## /sys
This is the system folder. It is created every time the systems boots and is used to interact with the kernel.

## /tmp
A temporary folder where application can stored temporary data.

## /usr
User application folder. These are not essential apps that can be reside in the subfolders such as /usr/bin or /usr/local

## /var 
Contains files and folders that are expected to grow in size. For example, log files.