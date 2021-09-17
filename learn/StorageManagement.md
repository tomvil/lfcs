# Chapter 6. Storage Management

## ## List, create, delete, and modify physical storage partitions
Modern, data driven systems today require more and more storage in order to provide the services they were designed to deliver.
A Linux system administrator should know how to see what storage is available on a system and how to manage it.

**fdisk**
  - Utility used to manage disk partitions.
  - 2TB partition size limit.
  - Can't grow or shrink partitions
  - Commands:
    - `fdisk /dev/sda` enter to /dev/sda block device configuration
    - `p` print all available partitions
    - `g` create new empty GPT partitions table
    - `n` create new partition
    - `d` delete partition
    - `t` set type of partition

**parted**
  - Another utility used to manage disk partitions.
  - Can grow or shrink a partition
  - Can handle partitions > 2TB size
  - Commands:
    - `parted /dev/sda` - enter to that block device configuration
    - `print` print all available partitions
    - `mkpart` create new partition
    - `resizepart` resize partition

**lsblk**
  - Disk management tool
  - Utility used to list block devices
  - **blkid** (show UUID of block devices)

<br>

## ## Manage and configure LVM storage
Logical Volume Management allows you to join multiple physical disks together in such a way that they are presented to the operating system as a single device.

**Commands to check LVM configuration**
- List all physical volumes
  - `pvdisplay`
    - Can display information about specific device: `pvdisplay /dev/sda1`
  - `pvs`
- List all volume groupes
  - `vgdisplay`
    - Can display information about specific group: `vgdisplay vgname`
  - `vgs`
- List all Logical volumes
  - `lvdisplay`
    - Can display specific infourmane about logical volumes: `lvdisplay vgname/lvname`
  - `lvs`


**LVM Management Tools**
  - `pvcreate`
    - Utility to initialize device blocks into physical volume for LVM:
      - `pvcreate /dev/disk1 /dev/disk2`
  - `vgcreate`
    - Utility used to create a volume group from physical volumes
      - `vgcreate vgname /dev/disk1 /dev/disk2`
  - `lvcreate`
    - utility used to create a logical volume(s) from a VG:
      - `lvcreate --name lvname --size xxxM vgname`

**Tutorial how to create LV disk:**
1. Partition the disk/disks with fdisk. (`fdisk /dev/sdb`)
    1. Change partition type to Linux LVM (t and 8e code)
2. Create physical volume from the partitions created (`pvcreate /dev/sdb1 /dev/sdb2`)
    1. `pvdisplay` to list all physical volumes
3. Create volume group from the physical volumes created (`vgcreate vgname /dev/sdb1 /dev/sdb2`)
    1. vgdisplay to list all volume groups
4. Create logical volume from volumes groups. (`lvcreate --name lv01 --size 10G vgname`)
    1. `lvdisplay` to list all logical volumes.
5. Create a filesystem on that logical volume (`mkfs.ext4 /dev/vg01/lv01`)
6. Mount it! (`mount /dev/vg01/lv01 /mnt`)

**Tutorial how to extend LVM**
1. Create a new partition
2. Create physical volume from that partition
3. Extend volume group with that physical volume (`vgextent vgname /dev/partition`)
4. Extend logical volume from VG. (`lvextend -l EXTENT /path/to/lv`)
    4. EXTENTS can be found out with `vgdisplay` - `FREE PE / Size`

**Another useful commands**
  - `pvremove` to remove physical volume
  - `vgreduce` to reduce volume group
  - `vgremove` to remove volume group
  - `lvreduce` to reduce logical volume
  - `lvremove` to remove logical volume

<br>

## ## Create and configure encrypted storage
Protecting sensitive data can be challenging and simply restricting user privileges isn't always enough. Having storage that can be locked and unlocked at will offers another level of security.

**Requirements**
  - To use encrypted storage a kernel module must be loaded
    - Check if the kernel module is loaded
      - `grep -i config_dm_crypt /boot/config-$(uname -r)`
      - `CONFIG_DM_CRYPT=m` the output will look something like this if it's enabled
    - `modprobe dm_crypt` to load the kernel module
    - `echo dm_crypt >> /etc/modules-load.d/dm_crypt.conf` to load dm_crypt module automatically at boot
    - `yum install -y cryptsetup` install the software that is used to manage encrypted storage


**Commands**
  - To created an encrypted partition
    - `crypsetup -y luksFormat /path/to/partition`
  - To open an encyprted partition
    - `crypsetup luksOpen /path/to/partition`
  - To close an encrypted partition
    - `cryptsetup luksClose fs_name`

**Tutorial**
1. First we need to create an encrypted partition on already existing partition
    1. `cryptsetup -y luksFormat /dev/sdb4`
2. If we want to use it, we need to open it first.
    1. `cryptsetup luksOpen /dev/sdb4 some_name`
    2. `some_name` can be whatever you want and can be different each time
3. Create a filesystem on the encrypted partition
    1. `mkfs.ext4 /dev/mapper/some_name`
4. If we want to see the information we have to mount it
    1. `mount /dev/mapper/some_name /mnt`
5. After job is done we can unmount it
    1. `umount /dev/mapper/some_name` or just
    2. `umount /mnt`
6. And after all partition needs to be closed
    1. `cryptsetup luksClose some_name`


**Automount**
1. Create a file with password that will be used to open the encrypted partition
    1. `echo veryStrongPassword > /root/crypt_key`
2. Change the file permissions
    1. `chmod 400 /root/crypt_key`
3. Add the key to encrypted partition
    1. `crypsetup luksAddKey /dev/sdb4 /root/crypt_key`
4. Add entry to `/etc/crypttab`
    1. `/dev/sdb4 /mnt ext4 defaults 0 0`
5. Add entry to `/etc/fstab`
    1. `/dev/sdb4 /mnt ext4 defaults 0 0`
6. Reboot the system and check if everything encrypted partition is mounted.

<br>

## ## Configure systems to mount file systems at or during boot
Linux filesystems are not like Windows. They require planning and consideration to determine what should be available at boot.
`/etc/fstab` file is used to mount filesystems at boot time.

**`/etc/fstab` file structure**
  - Device
    - Service/partition that contains the filesystem
  - Mount Point
    - Directory where the file system will be available
  - File system type
    - Type of the file system
  - Options
    - Mount options to acces to the device/partition
    - Available options:
      - `async` -> I/O asincrono
      - `auto` -> Can be mounted using mount -a
      - `default` -> Equal to this list of options: async,auto,dev,exec,nouser,rw,suid
      - `loop` -> To mount an ISO image
      - `noexec` -> no exec
      - `nouser` -> A user cannot mount this volume
      - `remount` -> Mount volume also if it is already mounted
      - `ro` -> Read only
      - `rw` -> Read an write
      - `relatime` -> Modify file access time (atime) if file is changed or one time a day. Alternative, to reduce disk traffic, noatime can be used. This is useful with SSD to avoid not useful write.
  - Dump
    - Enable/disable backing up of the device/partition. Usually set to 0.
  - Pass number
    - Controls order in which `fsck` checks partitions for errors at boot.
    - The root device should be `1`, all other should be disabled (`0`) or set to `2` to be checked after the root filesystem
  - Example:
    - `/dev/sdb1 /mnt/mountpoint ext4 defaults 0 0`

**Tutorial**
1. Find UUID of partition that you want to mount (you can use the partition name instead `/dev/sdb1` as example)
    1. `blkid` will print out all devices with UUID displayed
2. Open `/etc/fstab` and add the following line:
    1. `UUID(or partition name) /mnt ext4 defaults 0 0`
3. To check if everything is good you can type `mount -a` (will mount all filesystems from `/etc/fstab`)

**Useful commands**
  - `mount`
    - Shows all mounted volumes
  - `mount -a`
    - Reloads fstab
  - `mount -t type -o options device dir`
    - Mounts `device` with filesystem of `type` type on `dir` directory with provided options.


**SMB protocol**
  - `yum -y install samba-client cifs-utils`
    - Will install all CIFS/SMB related packages
  - `smbclient -L target_ip_address`
    - Will list all SMB shared directories available on a target IP address
  - `mount -t cifs -o username=smbuser,password=smbpass //192.168.0.10/share /media/samba`
    - Will mount remote (shared by 192.168.0.10) directory `shared` on `/media/samba` directory.
  - Example to make it permanent mount:
    - `echo 'username=smbuser' >> /root/.smbconf`
    - `echo 'password=smbpass' >> /root/.smbconf`
    - `chmod 600 /root/.smbconf`
    - Add the following line to `/etc/fstab`
      - `//192.168.0.10/share /media/samba cifs credentials=/root/.smbconf,defaults 0 0`

**NFS Protocol**
  - `yum -y install nfs-utils`
    - Will install software needed to manage NFS protocol
  - `showmount -e target_ip_address`
    - Will list all shared directories available on target ip address
  - `mount -f nfs -o defaults 192.168.0.10:/srv/nfs /media/nfs`
    - Will mount remote (shared by 192.168.0.10) directory `/srv/nfs` on `/media/nfs` dir.
  - Permanent configuration example:
    - Insert the following line to `/etc/fstab`
      - `192.168.0.10:/srv/nfs /media/nfs nfs defaults 0 0`
    - To use nfsv3:
      - `192.168.0.10:/srv/nfs /media/nfs defaults,vers=3 0 0`

<br>

## ## Configure and manage SWAP space
Swap space is used when the kernel needs more RAM, but no more RAM is available.

The kernel will "swap" pages of memory out to disk in either a swap file or swap partition in order to free up RAM.

**What is SWAP?**
  - A file, partition, or combination of the two, used to store inactive pages of memory to free up RAM for system use.
  - Not an alternative to RAM. Just a temporary fix as swap is much slower because the pages are written to a disk and no longer resident in memory.


**How much swap?**
  - For modern distributions, this answer will vary based on who you ask and the role of the system.
  - RedHat:
    - For a system with <2GB RAM, equal to or twice the amount of RAM.
    - For a system with with 4GB+, 20% of the size of the RAM is recommended.
  - CentOS:
    - For a system with <2GB RAM, swap should be twice of the size of RAM.
    - For a system with >2GB RAM, then the swap should be RAM size + 2GB
  - Ubuntu:
    - For a system with <1GB RAM, equal amount of RAM, no more than double.
    - If RAM is >1GB RAM, swap should be equal to the square of the RAM size, no more than double RAM size.
      - If you're using hibernation, it should be equal to RAM size + the RAM size's square root.

**Swap commands**
  - `swapon -a`
    - Will enable all swap files/partitions
  - `swapoff -a`
    - Will disable all swap files/partitions
  - `swapon --show`
    - Will show active swaps

**Create a SWAP file**
  - `fallocate -l 2G /swapfile1` first method
  - `dd if=/dev/zero of=/swapfile1 bs=1024M count=2` second method
  - `chmod 600 /swapfile1`
  - `mkswap /swapfile1`
  - `swapon /swapfile1`
  - Add the following line to `/etc/fstab` to make perma mount
    - `/swapfile1 swap swap defaults 0 0`

**Useful commands**
  - `free`
    - Check memory usage, swap will be displayed as well
  - `cat /proc/swaps`

<br>

## ## Create and manage RAID devices
LVM (logical Volume Management) allows Linux sysadmin to combine disks and present them as a single storage device. RAID (Redundant Array of Independent Disks) is another option that will combine disk and present them as a single device. It also offers redundancy to help guarantee file durability.

**Types of RAID**
  - **RAID0 (striping)**
    - Min number of disks: 2
    - Fault tolerance: None
    - Disk space overhead: None
    - Example:
      - `mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=2 /dev/sdb1 /dev/sdc1`
  - **RAID1 (mirroring)**
    - Min number of disks: 2
    - Fault tolerance: 1 Disk
    - Disk space overhead: 50%
    - Example:
      - `mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1`
  - **RAID10 (1+0)**
    - Min number of disks: 4
    - Fault tolerance: 1 Disk
    - Disk space overhead: 50%
    - Example:
      - `mdadm --create --verbose /dev/md0 --level=10 --raid-devices=4 /dev/sd[b-e]1 --spare-devices=1 /dev/sdf1`
  - **RAID5 (n+1)**
    - Min number of disks: 3
    - Fault tolerance: 1 Disk
    - Disk space overhead: 2 Disks
    - Example:
      - `mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1 --spare-devices=1 /dev/sde1`
  - **RAID50 (5+0)**
    - Min number of disks: 6
    - Fault tolerance: 1 disk
    - Disk space overhead: 2 disks
  - **RAID6 (dual parity)**
    - Min number of disks: 4
    - Fault tolerance: 2 disks
    - Disk space overhead: 2 disks
    - Example:
      - `mdadm --create --verbose /dev/md0 --level=6 --raid-devices=4 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde --spare-devices=1 /dev/sdf1`
  - **RAID 60 (6+0)**
    - Min number of disks: 8
    - Fault tolerance: 2 disk
    - Disk space overhead: 4 disk

**Install mdadm tools**
  - `yum install -y mdadm`

**How to create RAID (software)**
1. fdisk `/dev/sdX`
    1. Create two partitions
    2. Type should be Linux raid auto (`fd`)
2. Use `mdadm` utility to create RAID
    1. `mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sdX1 /dev/sdX2`
3. Check information about RAID
    1. `cat /proc/mdstat`
    2. `mdadm --details /dev/md0`
4. Create a filesystem on it
    1. `mkfs.ext4 /dev/md0`
5. Mount it!
    1. `mkdir /dev/raid`
    2. `mount /dev/md0 /mnt/raid`
6. Extend the RAID with new disk/partition
    1. `mdadm /dev/md0 --add /dev/sdX3`
    2. `mdadm --grow --raid-devices=3 /dev/md0`
7. Remove disk from RAID
    1. `mdadm /dev/md0 --fail /dev/sdX3 --remove /dev/sdX3`
    2. `mdadm --grow /dev/md0 --raid-devices=2 /dev/md0`


**How to mount RAID permanently**
1. Get information about RAID array
    1. `mdadm --detail --scan`
2. Copy it to `/etc/mdadm.conf`
3. Check if everything is OK with the configuration
    1. `mdadm --assemble --scan`
4. Run `dracut` to regenerate `initramfs`
5. Add entry in `/etc/fstab`

**How to delete RAID (software)**
1. `mdadm --stop dev/md0`
2. `mdadm --remove /dev/md0`
3. `mdadm --zero-superblock /dev/sdX1 /dev/sdX2`
4. To verify `cat /proc/mdstat`

**Useful commands**
  - `mdadm --details /dev/md0`
    - show status of RAID device
  - `mdadm --assemble --scan`

<br>

## ## Configure systems to mount file systems on demand
1. Install software need to manage automount
  1. `yum -y install autofs`
2. Edit `/etc/auto.master` and insert:
  1. `/media /etc/nfs.misc --timeout=60`
3. Edit `/etc/nfs.misc` and insert:
  1. `nfs -fstype=nfs 192.168.0.10:/srv/nfs`
4. `systemctl start autofs`

<br>

## ## Create, manage and diagnose advanced file system permissions
To meet unusual permissions requirements and/or situations, a Linux system administrator has additional options available:
  - `setuid`
    - Permission set on a file so that when an executable is launched, it will run with the owners privileges rather than the user.
    - `chmod 4774 filename.sh`
  - `setgid`
    - When set on executable, instead of running with the privileges of the group of the user who executed it, it will run with those of the group which owns the file.
    - When set on directory it changes the default behaviour so that the group of the files created inside the directory will not be that of the user who created them but, that of the group on the parent directory itself.
    - `chmod 2574 /home/directory`
  - `sticky bit`
    - Permission set on a directory that allows only the owner or the root user to delete or rename contents within the directory.
    - `chmod 1744 /home/user/Documents`

**Find all directories with GUID bit set**
  - `find / -type d -perm -2000`

**Extended attributes**
  - They are file properties
  - Only root user can remove the attribute
  - `chattr +i filename`
    - Will add `immutable` attribute to a file.
    - The file cannot be deleted or removed
  - `chattr -i filename`
    - Will remove `immutable` attribuet
  - `lsattr`
    - Will show files extended attributes

<br>

## ## Setup user and group disk quotas for filesystems
When disk space is much less expensive today than it used to be, if you have a lot of users or a highly utilized solution, you may find that you need to limit the amount of storage used by a suer or group of users.

**Install quota utilities**
1. `yum install -y quota`

**How to enable User and Group Quotas**
1. Update `/etc/fstab`
  1. Edit mount point and add `usrquota` and/or `grpquota` options to that entry
2. Run the `quotacheck` command to create used and/or group quote files
3. Use the `quotaon` command to turn on quotas for a filesystem.

**Check if quota modules are loaded**
1. `find /lib/modules/`uname -r` -type f -name '*quota_v*.ko*'`

**Quota Management**
  - Use `edquota` to create quotas
    - `edquota -u username`
    - `edquota -g groupname`
    - `setquota -u username 150M 200M 0 0 /mnt/data`
  - View specific quota entries:
    - `quota -vs username`
    - `quota -vgs groupname`
  - To run a quota report
    - `repquota -asgu`
    - This will return information on all mounts with quotas, in a human-readable format and will provide a report on users as well as groups.

**How to disable quota**
1. `quotaoff`
2. Remove entries in `/etc/fstab`
3. Set all `0` in `edquota` or `setquota`

<br>

## ## Create and configure file systems
**How to create filesystem**
  - `mkfs`
    - `mkfs -t ext4 /dev/sda1`
  - `mkfs.format`
    - `mkfs.ext4 /dev/sda1`

**Check mounted filesystems**
  - `lsblk -f`
  - `mount | grep "^/dev"`
