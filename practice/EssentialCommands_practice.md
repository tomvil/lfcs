# Chapter 1. Essential Commands. Practice.

## ## Log into local & remote graphical and text mode consoles

1. Login into remote machine with text based console via ssh.
2. Login into remote machine with graphical display via ssh.

<br>

## ## Search for Files.
1. Search for all files in `/etc` directory which names ends with `.conf`
2. Search for files and directories in `/etc` directory that has `777` permissions. Remove those files.
3. Search for directories and files that are larget than 1 Megabyte.
4. Search for files that are under 500 Kilobyte of size.
5. Search for `dnsmasq.conf` file
6. Search for `DRACUT.conf` file with insensitive case search.
7. Search for files that were modified less than 20 minutes ago
8. Search for files that were modified more than 20 days ago
9. Search for files that were modified less than 20 days ago
10. Search for files that were accessed more than 35 minutes ago
11. Search for files that were changed less than 5 days ago
12. Search for directory named `cups`
13. Search for file named 'squid.conf` while descending at most 3 levels of directories.
14. Search for files that has same inode as `/etc/dnsmasq.conf` 
15. Search for files that are owned by root
16. Search for files that are not owned by root
17. Search for files that have `777` permissions
18. Locate where is command `passwd` executable
19. Check what of type is `echo` command

<br>

## ## Compare and manipulate file content.

1. View `/etc/cups/snmp.conf` file content
2. Check `/var/log/message` output continiously with two different commands.
3. View `/etc/radnd.conf` file with line numbers.
4. Generate a file with numbers from 1 to 10 and sort it in reverse
5. Generate a file with numbers from 10 to 1 and sort it
6. Generate a two column file and sort it by second word.
7. Generate a a file with two same rows (each after another) and remove duplicated line.
8. Create an empty linux file
9. Print first word of each line of `/etc/dnsmasq.conf` file
10. Print first and third word of each line of `/etc/dnsmasq.conf` file.
11. Print 10 last lines of `/var/log/messages` file
12. Print 5 last lines of `/var/log/messages` file
13. Print 10 first lines of `/var/log/message` file
14. Print 3 first lines of `/var/log/messages` file
15. Change `##` to `**` in `/etc/sudoers file (using vi and tr). Do not save.
16. Generate two almost identical files (can change one word or etc.) and: <br>
  16.1. Check the difference between those two files <br>
  16.2. Check the difference between those two files in context mode output <br>
  16.3. Check the difference between those two files in two column output <br>
17. Check difference between two random directories (e.g. `/var/log/audit /var/log/cups`)

<br>

## ## Use input-output redirection
1. List `/etc` directory and redirect output to `/tmp/etc_opt.txt` file
2. List `/opt` directory and append output to `/tmp/etc_opt.txt` file
3. Pass `/tmp/etc_opt.txt` file content to `less` command (while using pipe)
4. Pass `/tmp/etc_opt.txt` file content to `less` command using stdin redirect
5. Run `find /proc -name "cpu*"` as non-root and redirect stderr to `/dev/null`
6. Run the same command as 4 but redirect error to stdout and then to `/dev/null`

<br>

## ## Analyze text using basic regular expressions.
1. List all files/directories in `/etc` that begins with letter `a`
2. List all files/directories in `/etc` that begins with letter `a` and is made of two characters
3. List all files/directories in `/etc` that is named `aa` or `ab`
4. List all files/directories in `/etc` that is named `aa`, `ab` or `ac`
5. Search for pattern `yum.log` in `/etc/logrotate.d/` directory
6. Search for pattern `sudoers` in `/etc` directory and all subdirectories as well.
7. Seach for pattern `SUDOERS` in `/etc` directory and all subdirectories as well. Case insensitive.
8. Search for every line that stars with `#Max` in `/etc/systemd/journald.conf` file
9. Search for every line that starts with `#R` but second letter is not equal to `a` in `/etc/systemd/journald.conf`
10. Search for all The or the (exact match, no `then` or etc included) words in `/etc/dnsmasq.conf`
11. Use sed to change `TRUE` to `FALSE` in `/etc/default/nss` file (only first occurance).
12. Use sed to change `TRUE` to `FALSE` in `/etc/default/nss` file (all occurances).
13. Use sed to change `true` to `false` in `/etc/default/nss` file (all occurances, case insensitive).
14. Use sed to change only line 12 `true` to `false` value (case insensitive)
15. Use sed to print all rows that contains value `TRUE` in `/etc/default/nss`
16. Use sed to print lines from 6 to 10 in `/etc/dnsmasq.conf` file.
17. Use sed to delete all lines that contains `False` in `/etc/selinux/semanage.conf` file
18. Use sed to delete row number 5 from `/etc/default/nss`
19. Use sed to insert text `very cool` to line 3 in `/etc/default/nss` file.
20. Change `TRUE` to `FALSE` in `/etc/default/nss` file. And keep copy of a original file.

<br>

## ## Archive, Backup, Compress, Unpack and Decompress files.
1. Create a tarball from `/opt` directory to `/root` directory
2. Create a compressed tar file (gzip) from `/opt` directory to `/root` directory
3. Extract gzip created in task number two
4. Create a compressed tar file using bzip2 `/opt`
5. Extract bzip2 created in task number four
6. View contents of tarball
7. Remove a file from tarball
8. Update a file in tarball
9. Create a backup of device
10. Restore a backup to device
11. Synchronize two directories between two remote devices.

<br>

## ## Create, delete, copy and move files and directories
1. Create a file named `testing` (using touch, echo and texteditor)
2. Create a directory named `stuff`
3. Create a subdirectory `my/personal/photos` in recently created `stuff` directory
4. Copy `/etc/dnsmasq.conf` to `stuff` directory
5. Copy `/etc/audit` directory to `stuff` directory
6. Rename `stuff/dnsmasq.conf` file to `dnsmasq.conf.backup`
7. Rename `stuff/audit` directory to `audit.backup`
8. Delete `stuff/dnsmasq.conf.backup` file
9. Delete `stuff/audit.backup` directory

<br>

## ## Create and manage hard and soft links
1. Create a soft link from `/etc/dnsmasq.conf` to your home directory (name it dns.conf)
2. Create a hard link from `/etc/dns/resolv.conf` to your home directory (leave the name as it is)
3. List files and check inode numbers.

<br>

## ## List, set and change standard file permissions
Create `personal` directory in your `$HOME`. Create file named `important.conf` in that directory.

1. Grant full access on `personal/important.conf` for everyone
2. Remove execute permission on `personal/important.conf` for groups only.
3. Remove all permissions for others on `personal/important.conf`
4. Set `suid` for `/sbin/ip` and keep current permissions
5. Set `sgid` bit for `personal` directory and keep current permissions
6. set sticky bit on `personal/important.conf` and keep current permissions
7. Change `/etc/rsyncd.conf` permissions to all for everyone and set sticky bit
8. Change `personal/important.conf` owner to some other user
9. Change `personal/important.conf` group to `wheel`
10. Change both user and group to `root` for `personal/important.conf` file
11. Set `/etc/resolv.conf` immutable and test if it was added. (Remove after)
<br>

## ## Read and use system documentation
1. Get help for `traceroute` command
2. Open full manual for `sed` command
3. Search for all available manuals by `time` key

<br>

## ## Manage access to the root account
1. Become a root user
2. Become some other user
3. Run a command with root privileges
4. edit `sudoers` file and add some random user to sudoers group, so he can use ALL comands as all users
5. do the same as 4 just for group.
