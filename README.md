# Linux
It will cover the basics of Linux which is a must for every Computer student.
For example, Becoming an administrator of IT infrastructure and also important for developers of any language.


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
