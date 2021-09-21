# Basic Linux System Commands
Table of contents
- [Basic Linux System Commands](#basic-linux-system-commands)
  - [1. Processes and Services](#1-processes-and-services)
  - [2. Linux Version and System Info](#2-linux-version-and-system-info)
  - [3. Disk and Device Properties](#3-disk-and-device-properties)
  - [4. Terminal multiplexer](#4-terminal-multiplexer)
  - [5. System and hardware information](#5-system-and-hardware-information)

## 1. Processes and Services
* List processes

`top`	 -> list of running services, in real time

`ps aux` -> list of running processes, not real-time

Filter results with grep:
`ps aux | grep apache2`

* Process ID

The process id (PID) can be used to identify or kill an service: 
```
pgrep <service_name> 		
kill <process_id> <process_id> <process_id>
``` 
Or kill all related processes in one command:
`killall <service_name>` 

* Start/Stop/Restart/Status

Classic commands:
```
sudo service <servie_name> start
sudo service <servie_name> stop
sudo service <servie_name> restart
sudo service <servie_name> status
```
More recent system control commands:
```
sudo systemctl start <service_name>
sudo systemctl stop <service_name>
sudo systemctl restart <service_name>
sudo systemctl status <service_name>
```
Using the path:
`sudo /etc/init.d/<service_name> start`
* Reboot and shutdown
```
sudo reboot
sudo shutdown -h -<minutes>		//-> shutdown system in x minutes.
sudo shutdown -c				//-> to cancel the previously set shutdown
sudo shutdown -h //-> halt and power off the system
sudo shutdown -h now //-> halt the system now instead of the default in 1 minute
sudo shutdown -r //-> shutdown and reboot
```

## 2. Linux Version and System Info
```
uname -a //-> full kernel version info for the system
uname -v //-> version
uname -r //-> release

lsb_release -a  //-> ubuntu release version info

cat /etc/os-release
cat /etc/lsb_release
#operating system release files in the /etc directory.

hostnamectl
#change hostname of the machine
hostnamectl set-hostname [new name]

date
#or
timedatectl
#date is displayed in UTC

sudo dpkg-reconfigure tzdata
#or
timedatectl list-timezones
timedatectl set-timezone [timezone]
#reconfigure timezone in the data package
```

## 3. Disk and Device Properties
* Check free disk space (all filesystems, human readable):
`df -ah`

* Show all mounted devices (uuid of all drives and partitions): 
`sudo blkid` 

* Mount a new drive on the system:
```
mount		//-> show all mounted drives
ls /mnt		//-> the folder where mounted devices live 
mount /dev/sda3 /mnt
```
The file responsible for automatic mount at boot is FSTAB in the /etc folder:
`less /etc/fstab`

* Check temperature of CPU and HDD
To do so third party software is required: lm-sensors and hddtemp
```
sudo apt-get install lm-sensors hddtemp

hddtemp /dev/sda
```

## 4. Terminal multiplexer
When connecting remotely via ssh the connection can be interrupted. By itself this is not a big issue. But when a shell is terminated all programs that were running in that shell are also interrupted. When doing important updates remotely an interruption can thus leave the system in a broken state.

The mitigate this risk a terminal multiplexer is used via the command `tmux`. This opens up a virtual session on the server machine to which you can connect and disconnect at will. Thus when the connection is lost, the session continues and you can reattach to the existing session after re-connecting using the command `tmux attach`.

Tmux is also useful to allow you to disconnect when you know a process will take a lot of time to complete.

Screen is a similar application that allows you to do multitasking via the command line.

## 5. System and hardware information
* list all hardware devices
```
sudo lshw
sudo lshw -businfo
sudo lshw -businfo -c disk
#show only businfo, device class (disk)
```

* list block storage devices
This returns info about harddrives and loops devices, which are files that contain filesystem
```
lsblk
blkid //-> additional info
```

* list memory and cpu info
```
lsmem -a
lscpu

cat /proc/cpuinfo
#check cpuinfo file
```
* list other devices
```
lspci
lspci -tv //-> tree view
lspci -v  //-> detailled view

lsusb
lsusb -tv //-> tree view
lsusb -v  //-> detailled view
```