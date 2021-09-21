# Basic Linux System Commands
Table of contents
- [Basic Linux System Commands](#basic-linux-system-commands)
  - [1. Processes and Services](#1-processes-and-services)
  - [2. Linux Version Info](#2-linux-version-info)
  - [3. Disk and Device Properties](#3-disk-and-device-properties)

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
```

## 2. Linux Version Info
```
uname -a //-> full kernel version info for the system
uname -v //-> version
uname -r //-> release
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