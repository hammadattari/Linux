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
## When adding a new hard drive to an Ubuntu server and creating LVM (Logical Volume Manager) on it, follow these steps:

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
6. ### Add the LV to the /etc/fstab for persistent mount:
    - sudo lsblk -o +UUID (get the UUID of the LV)
    - sudo UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\t/mnt/data\text4\tdefaults\t0\t0 (\t means press a tab)
    - e.g. UUID=30dbec69-b3d0-4ac3-9619-cf4a534bca09       /mnt    ext4    defaults        0       0
7. ### Verify the deletion or mounted on /mnt
    - df -h
    - ls -al /mnt (files are shown)

![carbon (8)](https://github.com/hammadattari/Linux/assets/44769452/d2a6082a-d47e-43a3-ba94-2048a80e07e7)
## Extending Root Partition with LVM

### Table of Contents

1. **Verify Partition on New Disk (/dev/sdd):**  This step checks for the newly created partition.
2. **Create Physical Volume (PV):**  This command creates a PV using the partition.
3. **Extend Volume Group (VG):**  This step expands the VG to include the new PV.
4. **Identify Root Logical Volume (LV):**  Locate your root LV for resizing.
5. **Resize the Logical Volume (LV):**  This command extends the LV using available free space.
6. **Verify Available Space (Optional):**  (Optional step) Check if unallocated space is visible within the partition.
7. **Identify Filesystem Type:**  Determine the filesystem type of your root partition (e.g., ext4, xfs).
8. **Resize the Filesystem:**  Use the appropriate command (ext4: `resize2fs`, xfs: `xfs_growfs`) to resize the filesystem and utilize the extended space.
9. **Verify Resize (Optional):**  (Optional step) Confirm that the size of your root partition has increased.


**Verify Partition on New Disk**

```
fdisk -l /dev/sdd

# Note the whole disk 5GB partitioned with name: /dev/sdd1
```

**Create Physical Volume (PV)**

```
pvcreate /dev/sdd1
```

**Extend Volume Group (VG)**

Add the new physical volume to the existing volume group  :

```
vgextend <your_vg_name> /dev/sdd1

# Example: ubuntu-vg is root volum group
vgextend ubuntu-vg /dev/sdd1
```

**Identify Logical Volume (LV)**

List all logical volumes and locate the root LV:

```
lvdisplay
```

**Resize Logical Volume (LV)**

* Copy the path to the LV you want to extend (e.g., /dev/ubuntu-vg/ubuntu-lv)
* Extend the logical volume:

```
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv or
lvextend -L +5G /dev/mapper/ubuntu--vg-ubuntu--lv

```

**Resize Filesystem**

1. Check the disk usage:

```
lsblk
df -h
```

2. **Resize the Filesystem:**

```
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

3. Verify the updated disk usage:

```
df -h

# Example output:

# Note: The available space has increased from 24GB to 29GB.
```




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
e.g. lvremove  /dev/mapper/myvg-mylv
````
Replace "my_partition" with the actual name of the partition.

#### Step 6: Remove the volume group (VG)

If the partition was part of a VG, you need to remove the VG as well. Run the command:
```
sudo vgremove my_vg
e.g. vgremove myvg
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
