# Chapter 6. Storage Management

## ## List, create, delete, and modify physical storage partitions
1. Enter to any block device and perform below tasks (fdisk)
    1. Print all available partitions
    2. Create new empty GPT partition table
    3. Create new partition
    4. Delete partition
    5. Set random types to the partitions
    6. Do not save the changes nad quit
2. Do the same with parted
3. List all block devices
4. List UUID of all block devices.

<br>

## ## Manage and configure LVM storage
1. Configure LV disk from two different partitions
2. Add thirt partition to created LV and extend it to maximum
3. Display information about physical volumes
4. Display information about volumes groups
5. Display information about logical volumes
6. Delete logical volume

<br>

## ## Create and configure encrypted storage
1. Check if the module is loaded
    1. Load it if not.
2. Create ant encrypted partition
3. Open encrypted partition
4. Create a filesystem on the encrypted partition
5. Mount encrypted partition
6. Create automount and check if everything is working fine
7. Unmount and close the partition.

<br>

## ## Configure systems to mount file systems at or during boot
1. Create image with `dd`
2. Add filesystem on it
3. Configure `fstab` to mound that filesystem on boot.
4. Install samba
    1. Add automount for samba shared directory
5. Install nfs
    1. Add automount for nfs shared directory`
6. List all mounted devices

<br>

## ## Configure and manage SWAP space
1. Disable all swap files/partitions
2. Enable all swap files/partitions
3. List all active swaps
4. Create a swap file
5. Mount it
6. Configure fstab to mount it on boot.
7. Check memory

<br>

## ## Create and manage RAID devices
1. Create RAID1
2. Configure fstab to mount it on boot. Test it.
3. Delete RAID1

<br>

## ## Configure systems to mount file systems on demand
1. Configure to mount filesystem using `autofs`

<br>

## ## Create, manage and diagnose advanced file system permissions
1. Create a file and set `setuid` on it. Test it.
2. Create a directory and set `setgid` on it. Test it.
3. Create a directory and set `sticky bit` on it. Test it
4. Find all files with `setuid`
5. Find all files with `setgid`
6. Find all files with `sticky bit`
7. Create a file and add immutable attribute on it. Test it.
8. Show extended attributes of that file.

<br>

## ## Setup user and group disk quotas for filesystems
1. Check if quota module is loaded
2. Enable user and group quotas on any filesystem
3. Set quotas for specific user
4. Set quotas for specific group
5. View quotas for specific user
6. View quotas for specific group
6. Generate quota report
7. Disable quota

<br>

## ## Create and configure file systems
1. Create image with `dd`
2. Add filesystem on it
3. Mount it
4. Check mounted filesystems
