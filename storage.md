# Storage

table of contents
- [Storage](#storage)
  - [Get info](#get-info)
  - [Configuring a disk](#configuring-a-disk)
    - [Create partitions with FDISK](#create-partitions-with-fdisk)
    - [Create a file system](#create-a-file-system)
    - [Setup user permission for new file system](#setup-user-permission-for-new-file-system)
  - [Automatically mount devices](#automatically-mount-devices)
  - [Monitor file storage activity](#monitor-file-storage-activity)
  - [Redundant storage options](#redundant-storage-options)

## Get info
```
# Check free disk space (all file systems, human readable):
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

## Automatically mount devices
The file system table FSTAB is responsible for the automatic mount at boot. 
`/etc/fstab` is a configuration file that can be edited to adjust the options. Using the partition identifier is not a good choice because this might change due to detection order changes. Better to include the Universally Unique Identifier (UUID) that can be fetched and copied from `blkid`. Thus use: `/dev/disk/by-uuid/<uuid>`. Add the `nofail` options for USB drives so that the system can continue even if the USB fails.

## Monitor file storage activity
A program that can be used is `iotop` and can be used in combination with `sysstat/iostat`. The programs monitor input-output activity on devices.
```
sudo apt install iotop

sudo apt install sysstat

sudo iostat -h

sudo hdparm -tTv /dev/sda
//-> checks the read/write speed of the volume
```

## Redundant storage options
There are mainly two reasons for redundant storage: availability and protection against disk failure. Usually the system critical files are stored on one disk and the file sharing media live on the second and additional disk in an array. Speed is usually a minor concern. Fault tolerance requirements are more important because some files need to available all the time.

Redundancy is not a backup. Usually a backup is stored away from the physical location on another device.

There are various solutions available, each requires further in depth study.

- RAID: Redundant Array of Independent Disks. This is the classic redundancy solution that is simple to implement. It uses *mirroring* to store the same data on at least two separate disks or *striping* to divide files over multiple volumes. RAID has various levels: level 0 is just a bunch of disks (jbod) and does not provide redundancy, it means that at least two disks are combined to form one volume and disk are filled progressively. Level 1 is mirroring. Etc. The software program used for this purpose is `mdadm`.

- LVM is the Logical Volume Manager and offers a slightly more complex approach. Physical Volumes (PV) are grouped together in Volume Groups (VG). Logical Volumes (LV) are created within groups and can span over multiple physical disks. Moving the LV's around VG's and PV's can offer extra flexibility though it adds complexity.

- ZFS combines a file system and a volume manager. Physical disks are combined into Virtual Devices (Vdev's). It uses copy-on-write (COW) and therefore is a populair choice for data protection as it can easily create and restore snapshots. An application for ZFS is the server OS FreeNAS.

- B-Tree File System (BTFS). It is resilient against data corruption as it combines file integrity management with RAID features. It can be used on a single disk.