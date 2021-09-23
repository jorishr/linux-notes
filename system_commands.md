# Basic Linux System Commands
Table of contents
- [Basic Linux System Commands](#basic-linux-system-commands)
  - [1. Shutdown and reboot](#1-shutdown-and-reboot)
  - [2. Linux Version and System Info](#2-linux-version-and-system-info)
  - [3. System and hardware information](#3-system-and-hardware-information)
  - [4. Logs](#4-logs)
  - [5. Terminal multiplexer](#5-terminal-multiplexer)

## 1. Shutdown and reboot
```
sudo reboot

sudo shutdown -h -<minutes>		//-> shutdown system in x minutes.

sudo shutdown -c				      //-> to cancel the previously set shutdown

sudo shutdown -h              //-> halt and power off the system

sudo shutdown -h now          //-> halt the system now instead of the default in 1 minute

sudo shutdown -r              //-> shutdown and reboot
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


## 3. System and hardware information
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
* check temperature of CPU and HDD
To do so third party software is required: lm-sensors and hddtemp
```
sudo apt-get install lm-sensors hddtemp

hddtemp /dev/sda
```


## 4. Logs
Classic text based logs can be found in `/var/log/` and are managed by `rsyslog`. There format can be programmatically read but can become overwhelming when there are many logs.
Binary logs can be found accessed via `journalctl` command and are managed by `systemd-journald`, it is considered to be easier to query.
```
# system log
less /var/log/syslog    //-> use 'f' or 'b' to move up or down; use '/' to search
tail /var/log/syslog

# dpkg log for history of installing and removing packages
tail /var/log/dpkg.log

# firewall log
tail /var/log/ufw.log

# specify the units to focus on by using journalctl
journalctl -u 

journalctl --since "10 minutes ago"
journalctl --since "2021-09-22"

journalctl -b  //-> since last boot
```


## 5. Terminal multiplexer
When connecting remotely via ssh the connection can be interrupted. By itself this is not a big issue. But when a shell is terminated all programs that were running in that shell are also interrupted. When doing important updates remotely an interruption can thus leave the system in a broken state.

The mitigate this risk a terminal multiplexer is used via the command `tmux`. This opens up a virtual session on the server machine to which you can connect and disconnect at will. Thus when the connection is lost, the session continues and you can reattach to the existing session after re-connecting using the command `tmux attach`.

Tmux is also useful to allow you to disconnect when you know a process will take a lot of time to complete.

Screen is a similar application that allows you to do multitasking via the command line.