# Linux
It will cover the basics of Linux which is a must for every Computer student.
For example, Becoming an administrator of IT infrastructure and also important for developers of any language.
# Step-by-step Guide to Create New Partition when we Add new hard disk within Linux

![MindMapLVM](https://github.com/hammadattari/Linux/assets/44769452/6e159643-83a7-4bc7-8fa3-1f1af3fe1c8c)

1. ## Start fdisk:
    - sudo fdisk /dev/sdb (replace /dev/sdb with your disk device)
2. ## To create a new partition, use the following parameters:
- n (create a new partition)
- p (make it a primary partition)
- 1 (assign it as partition number 1)
- 2048 (set the first sector)
- +100G (specify the partition size)
3. ## Set the partition type to LVM:
 - t (change partition type)
 - 8e (LVM partition type)
4. ## Write changes:
  - w (make revisions)
## When adding a new hard drive to an Ubuntu server, the best way to create partitions with LVM (Logical Volume Manager) is to follow these steps:

1. ### Install LVM:
    - sudo apt-get install lvm2 (if not already installed)
2. ### Create a physical volume (PV):
    - sudo pvcreate /dev/sdb (replace /dev/sdb with your new disk device)
3. ###  Create a volume group (VG):
    - sudo vgcreate myvg /dev/sdb (replace myvg with your desired VG name)
4. ### Create logical volumes (LVs):
    - sudo lvcreate -n mylv -l +100%FREE  myvg
    - sudo lvcreate -n mylv -L 100G myvg (replace mylv with your desired LV name and 100G with your desired size)
5. ### Format and mount the LV:
    - sudo mkfs.ext4 /dev/myvg/mylv
    - sudo mount /dev/myvg/mylv /mnt
6. ### Add the LV to the fstabe for persistent mount:
    - sudo lsblk -o +UUID (get the UUID of the LV)
    - sudo UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\t/mnt/data\text4\tdefaults\t0\t0 (\t means press a tab)
    - e.g. UUID=30dbec69-b3d0-4ac3-9619-cf4a534bca09       /mnt    ext4    defaults        0       0
7. ### Verify the deletion or mounted on /mnt
    - df -h
    - ls -al /mnt (files are shown)

![carbon (8)](https://github.com/hammadattari/Linux/assets/44769452/d2a6082a-d47e-43a3-ba94-2048a80e07e7)

## Increasing Storage Space in an Existing LVM 

This guide outlines the steps to increase storage space in your existing LVM (Logical Volume Manager) setup. Remember to replace all names and paths with your specific configuration.  **Caution!** Incorrect commands can lead to data loss or corruption. If unsure, consult an expert or refer to your Linux distribution's LVM documentation.

### Prerequisites

* Existing LVM setup with free space available on a physical volume.
* New storage device (e.g., hard drive or SSD) attached to the system.
* Root access or ability to run `sudo` commands.

### Steps

1. **Check Current Configuration:**
   - Run `sudo lvm display` or `sudo lvmdiskscan` to view the current LVM layout and free space.
   - Identify the Volume Group (VG) name, Logical Volume (LV) name, and the new Physical Volume (PV) path (/dev/newdevice).

2. **Add a New Physical Volume:**
   - Run `sudo pvcreate /dev/newdevice` to create a physical volume from the new storage device.
   - Verify the new PV with `sudo pvs` or `sudo pvdisplay`.

3. **Extend the Volume Group:**
   - Run `sudo vgextend VolGroup /dev/newdevice` to extend the Volume Group (replace `VolGroup` with your actual VG name).
   - Verify the extension with `sudo vgs` or `sudo vgdisplay`.

4. **Extend the Logical Volume:**
   - Run `sudo lvextend -l +100%FREE /dev/VolGroup/LogicalVol` to extend the Logical Volume (replace `VolGroup` and `LogicalVol` with your actual names).
   - Verify the extension with `sudo lvs` or `sudo lvdisplay`.

5. **Resize the File System:**
   - For ext2, ext3, or ext4 file systems: `sudo resize2fs /dev/VolGroup/LogicalVol`
   - For XFS file system: `sudo xfs_growfs /dev/VolGroup/LogicalVol`

6. **Verify the Changes:**
   - Run `df -h` to confirm the increased storage space.
   - Run `sudo lvm display` to verify the updated LVM configuration.


# Only 9 commands to LVM Remove form Physical Partition

![carbon (5)](https://github.com/hammadattari/Linux/assets/44769452/4d245742-267d-40cd-a298-11ed8da84ce4)


### Only Remove LVM but  /dev/sdb1 is remains available for you. This is The Step by Step Style guide for Ubuntu Server.

#### Step 1: Backup your data
Before deleting any partitions, make sure to backup your important data to an external drive or cloud storage.

#### Step 2: List the LVM partitions and unmount partition

Open a terminal and run the command:
```
sudo lvdisplay


Sudo umount /mnt
```
This will list all the LVM partitions on your system.

#### Step 3: Identify the partition to delete

Note the name of the partition you want to delete (e.g., "my_partition").

#### Step 4: Deactivate the partition

Run the command:
```
sudo lvchange -an /dev/my_partition
e.g. lvchange -an /dev/mapper/myvg-mylv
```
Replace "my_partition" with the actual name of the partition.

#### Step 5: Remove the partition

Run the command:
```
sudo lvremove /dev/my_partition
````
Replace "my_partition" with the actual name of the partition.

#### Step 6: Remove the volume group (VG)

If the partition was part of a VG, you need to remove the VG as well. Run the command:
```
sudo vgremove my_vg
e.g. vgremove -an /dev/mapper/myvg-mylv
```
Replace "my_vg" with the actual name of the VG.

#### Step 7: Remove the physical volume (PV)

If the partition was on a PV, you need to remove the PV as well. Run the command:
```
sudo pvremove /dev/sda1
```
Replace "/dev/sda1" with the actual device path of the PV.

#### Step 8: Update the LVM configuration

Run the command:
```
sudo vgscan --mknodes
```
This will update the LVM configuration and remove any remaining references to the deleted partition.

#### Step 9: Verify the deletion

Run the command:
```
sudo lvdisplay
lsblk
```
