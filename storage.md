# Storage
## Get info
```
# Check free disk space (all filesystems, human readable):
df -ah

# List all mounted devices (uuid of all drives and partitions): 
lsblk
blkid 
```
The loop file systems that Snap uses can be ignored. What matters are the Serial Disks (sda, sdb, etc), that is the USB and SATA disks.

One disk can have multiple partitions.

To identify the specific model of any disk, fetch the businfo via the List Hardware command and limit the output to the Disk category:
```
sudo lshw -businfo -c disk
```
## Configuring a disk
It is recommended to use separate partitions for the system files and storage for services and users. The steps are similar for external and internal storage devices.

The steps are as follows: create partition(s); within a partition create a filesystem; setup permission in the filesystem.

### Create partitions with FDISK
```
sudo fdisk /dev/sda     //-> or /dev/sdb; double check you are working with the correct disk
```
Inside the fdisk program check the commands and instructions. Note that modifying partition tables is a destructive operation. So is creating a new file system.

### Create a file system
The ext4 is a recommended file system for server storage. 
```
sudo mkfs.ext4 /dev/sda1        //-> double check the correct partition

# The newly created file system needs to be attached to the root filesystem at some location by mounting the partition. Recommended is to use the /mnt/ folder.

sudo mkdir /mnt/storage
sudo mount /dev/sda1 /mnt/storage
```
### Setup user permission for new file system
By default only root has the permission to modify the files. Add all users through the `chmod` command. By doing so all users can read, write and modify the files that belong to them.
```
sudo chmod 777 /mnt/storage  
```
## Mount a new device

* Mount a new drive on the system:
```
mount		//-> show all mounted drives
ls /mnt		//-> the folder where mounted devices live 
mount /dev/sda3 /mnt
```

## Automatically mount devices
The file responsible for automatic mount at boot is FSTAB in the /etc folder:
`less /etc/fstab`
