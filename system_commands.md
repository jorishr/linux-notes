# Basic Linux System Commands
Table of contents
- [Basic Linux System Commands](#basic-linux-system-commands)
  - [1. Shutdown and reboot](#1-shutdown-and-reboot)
  - [2. Linux Version and System Info](#2-linux-version-and-system-info)
  - [3. System and hardware information](#3-system-and-hardware-information)
  - [4. Logs](#4-logs)

## 1. Shutdown and reboot
```bash
sudo reboot

sudo shutdown -h              # -> halt and power off the system

sudo shutdown -h -<minutes>		# -> shutdown system in x minutes.

sudo shutdown -h now          # -> halt the system now instead of the default in 1 minute

sudo shutdown -c				      # -> to cancel the previously set shutdown

sudo shutdown -r              # -> shutdown and reboot
```

## 2. Linux Version and System Info
```bash
uname -a # -> full kernel version info for the system
uname -v # -> version
uname -r # -> release

lsb_release -a  //-> Linux Standard Base Release info (Ubuntu 18.04 LTS, Codename: Bionic)

# Check operating system release files in the /etc directory:

cat /etc/os-release
cat /etc/lsb_release

# Check host machine info

hostnamectl

#change hostname of the machine

hostnamectl set-hostname [new name]

# Check the date on the machine
# Date is displayed in UTC

date
#or
timedatectl

# Adjust timezones in the data package

sudo dpkg-reconfigure tzdata
#or
timedatectl list-timezones
timedatectl set-timezone [timezone]
```

## 3. System and hardware information
* list all hardware devices
```bash
sudo lshw
sudo lshw -businfo
sudo lshw -businfo -c disk
# show only businfo, device class (disk)
```

* list block storage devices
This returns info about harddrives and loops devices, which are files that contain filesystem
```bash
lsblk
blkid # -> additional info
```

* list memory and cpu info
```bash 
lsmem -a
lscpu

cat /proc/cpuinfo
#check cpuinfo file
```
* list other devices
```bash
lspci
lspci -tv # -> tree view
lspci -v  # -> detailed view

lsusb
lsusb -tv # -> tree view
lsusb -v  # -> detailed view
```
* check temperature of CPU and HDD
To do so third party software is required: lm-sensors and hddtemp
```bash
sudo apt-get install lm-sensors hddtemp

hddtemp /dev/sda
```

## 4. Logs
Classic text based logs can be found in `/var/log/` and are managed by `rsyslog`. There format can be programmatically read but can become overwhelming when there are many logs.
Binary logs can be found accessed via `journalctl` command and are managed by `systemd-journald`, it is considered to be easier to query.
```bash
# system log
less /var/log/syslog    # -> use 'f' or 'b' to move up or down; use '/' to search
tail /var/log/syslog

# dpkg log for history of installing and removing packages
tail /var/log/dpkg.log

# firewall log
tail /var/log/ufw.log

# specify the units to focus on by using journalctl
journalctl -u 

journalctl --since "10 minutes ago"
journalctl --since "2021-09-22"

journalctl -b  # -> since last boot
```