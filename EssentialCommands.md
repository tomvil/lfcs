# Chapter 1. Essential Commands.
## ## Log into local & remote graphical and text mode consoles

**SSH (Secure Shell)** - Network communication protocol. Provides password or public-key based authentication and encrypts connections between two machines.

Login into remote machine with Text - based console:
  - `$ ssh root@192.168.144.12`
  - `$ ssh 192.168.144.12 -l root`

Login into remote machine with graphical display can be done via SSH:
  - `$ ssh -X root@192.168.144.12`

Or using any of software mentioned below:
  - There are many of software available that allow to connect to remote machine with graphical display. Few of them:
    - Remmina
    - VNC
    - RealVNC

Once you're logged in you can type `w` in the command line to see who is logged in, uptime and etc.
```
[cloud_user@635a80bdb51c ~]$ w
21:37:19 up 57 min,  1 user,  load average: 0.19, 0.06, 0.06
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
cloud_us pts/0    92.62.135.139    20:43    7.00s  0.22s  0.02s w
[cloud_user@635a80bdb51c ~]$
```

## ## Search for Files.
### ## FIND and LOCATE commands
**Find** - search for files in a directory hierarchy. It can be used to find files and directories, it can also execute commands on the findings (results).
It supports searching by file, directory name, creation/modification/access date, owner and permissions.

Basic syntax:
  - `$ find PATH_WHERE_TO_SEARCH PARAMETERS`

Search all files in `/etc` directory which names ends with `.conf` :
  - `$ find /etc -type f -name "*.conf"`

Search for files and directories in `/etc` directory that has `777` permissions and remove all the findings:
  - `$ find /etc -perm 777 -exec rm -rf {} \;`
    - `-exec` instructs that a command will follow
    - `{}` will be changed with the result of find query.
    - Command has to be inserted between `-exec` and `\;`.
      - `;` is used to identify the end of command character in bash shell. It has to be escaped with `\`.

Search for directories and files that is larger than 1 Megabyte.
  - `$ find / -size +1M` 

Search for files that are under 500Kilobyte of size
  - `$ find / -size -500k`

Search for file or directory with specific name:
  - `$ find / -name 'dnsmasq.conf'`

Search for file or directory with specific name but not case sensitive:
  - `$ find / -iname 'dnsmasq.conf'`

Search for files that were modified less than 20 minutes ago:
  - `$ find / -type f -mmin -20`

Search for files that were modified less than 20 days ago:
  - `$ find / -type f -mtime -20`

Search for files that were modified more than 20 days ago:
  - `$ find / -type f -mtime +20`

```
    - -amin/-atime searches for when the file was accessed
    - -cmin/-ctime searches for when the files was changed
```

Search for directory named `configs`:
  - `$ find / -type d -name 'configs'`

Search for file or directory named `keepalived.conf`, descending at most 2 levels of directories:
  - `$ find / -maxdepth 2 -type f -name 'keepalived.conf'`

Search all files and directories that have same inode of file:
  - `$ find / -samefile dnsmasq.conf`

Search all files and directories that are owned by root:
  - `$ find / -user root`

Search all files and directories that are NOT owned by root:
  - `$ find / ! -user root`

Search all files and directories with `777` permissions:
  - `$ find / -perm 777`

<br>

**Locate** - search for files in a directory hierarchy.
To use it the `find` database has to be up-to-date:
  - `$ updatedb`

Search for `dnsmasq.conf` file:
  - `$ locate "dnsmasq.conf"`

Search for `dnsmasq.conf` files but not case sensitive search:
  - `$ locate -i "dnsmasq.conf"`

<br>

### ## WHICH, WHEREIS, TYPE commands.

**which**
  - returns the location of a command bassed on the `PATH` settings.

```
$ which passwd
/usr/bin/passwd
```

**whereis**
  - returns the location of the binary, source file and man pages
  - will return multiple versions of a file if they exist.

```
$ whereis passwd
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```

**type**
  - returns information about the command type
  - details are based on how command relates to the shell configuration
  - `type -a` lists full data.

```
$ type -a echo
echo is a shell builtin
echo is /usr/bin/echo
```

<br>

## ## Evaluate and compare the basic file system features and options.
**Block device** - media used to store data (ssd, hdd, cd, usb, etc.).

**Filesystem** - method of allowing OS to interact with data on a block device.

**EXT (extended filesystem)**
  - EXT / 1992, created specifically for Linux
  - EXT2 / 1993, no journaling, but supported extended attributes and 2TB storage.
  - EXT3 / 2001, included journaling, in place upgrade from EXT2
  - EXT4 / 2008, backwards compatible, increased filesystem size to 1EB, increased max file size to 16TB, journal checksums and delayed allocation.

**BTRFS**
  - Often called BetterFS or ButterFS
  - Created by Oracle in 2007, stable in 2013.
  - Includes online FS expansion and reduction, read-only snapshots for faster backups, more efficient handling of small files and directory indexes
  - Online defragmentation and in place conversion of EXT FS
  - Considered by some to be the future replacement for EXT4

**ReiserFS**
  - Introduced in 2001
  - Offered several improvements over EXT4
  - Reiser4 was delivered in 2004 and was once thought to be the replacement for EXT4
  - Development stalled when the main developer was sent to prison in 2008

**ZFS**
  - Created by Sun Microsystems for Solaris. Now owned by Oracle.
  - Supports drive pooling, FS snapshots. filesystem striping.
  - Each file has a checksum, making it easy to tell if a file has been corrupted.
  - Made available under the CDDL which means it can't be included in the Linux kernel, but can be easily added.

**XFS**
  - Developed by Silicon Graphics for Irix in 1994. Ported to Linux in 2001.
  - Similar to EXT4 in many ways, with dynamic allocation and other features.
  - Can be expanded on the fly, but can't be reduced.
  - Handles large files well but does suffer performance issues with many small files.

**JFS**
  - Developed by IBM in 1990 for AIX. Ported to Linux in 1999
  - Offers good performance across small and large files, along with a small CPU footprint.
  - Many helpful features, but lacks the extensive Linux production testing that other filesystems have.

**SWAP**
  - Used to format a drive, but not technically a filesystem
  - Used for virtual memory (memory swapping) and doesn't have a viewable structure.
  - Temporary place for items in memory to be stored in low RAM situations.

**FAT/FAT32/exFAT**
  - Filesystem developed by Microsoft.
  - Does not offer journaling, but is supported by most OSes making it very compatible.
  - Cross-OS compatibility and lack of journaling make it a good use for USB drives or other media shared between systems.
  - exFAT is the best choice as it supports larger file and partition sizes.

<br>

## ## Compare and manipulate file content.
**cat**
  - Stands for concatenate.
  - Use this command to view the contents of a file.
  - Prints out the content of the file.

Example:
```
$ cat fuse.conf
# mount_max = 1000
# user_allow_other
```

**less** 
  - used to control the display of file contents.
  - `$ less -N filename` Show file output with line numbers
  - `$ less +F filename` Show output continiously (same as `tail -f`):

**sort**
  - `sort filename` sorts file A -> Z.
  - `sort -r filename` sorts file Z -> A (reverse).
  - `sort -k 2 filename` sorts file using second word as reference

**uniq**
  - Removes equal consecutive rows from file.
  - `uniq -w 2 filename` removes equal consecutive rows comparing only first two characters.
  - `uniq -c filename` removes equal consecutive rows and show number of occurences.

**touch**
  - Simple Linux command to update the access and modification times
  - Can be used to create an empty file.
  - `touch filename`

**cut**
  - `cut -d ' ' -f 1 filename` Prints first word of each line. Delimiter will be space.
  - `cut -d ' ' -f 1,3 filename` Prints first and third word of each line. Delimiter will be space.

**tail**
  - `tail filename` prints 10 last lines of the file
  - `tail -n 5 filename` prints 5 last lines of the file
  - `tail -f filename` Continiously monitors file output.

**head**
  - `head filename` prints 10 first lines of the file.
  - `-head -n 5 filename` prints 5 first lines of the file.

**tr**
  - `cat filename | tr true false` Will replace all occurrences of true with false.
  - `cat filename | tr -s ' '` Will replace all consecutive occurences of space with one space.

**nano/vi/vim**
  - Linux text editors.
  - `nano filename`
  - `vi filename`

Some shortcuts for `vi` editor, when file is opened:
  - `o` - add a new line after and enter insert mode.
  - `O` - add a new line above and enter insert mode.
  - `u` - undo
  - `ctrl+r` - redo
  - `gg` - go to start of the file
  - `G` - go to the end of the file
  - `dd` - delete whole line
  - `x` - delete character

To replace text in the file:
  - `:%s/true/false/g` - replace all occenreces of true with false.
  - `:%s/true/false` - replace only first occurence of true with false.

Copy/Paste:
  - `v` - visual mode (select multiple lines)
  - `y` - copy selected text
  - `p` - paste copied text
  - `d` - delete selected text

**diff**
  - Compares two files or directories line by line
  - Severial options for ignoring, filtering the comparison.
  - Output can be displayed in `ed` format, context mode or unified format.
  - `diff filename1 filename2` shows differences between filename and filename2
  - `diff -c filename1 filename2` does the same just output is in context mode
  - `diff -y filename1 filename2` does the same just the output is in two columns
  - `diff directory1 directory2` does the same just with directories

**comm**
  - Compare two sorted files line by line
  - Output is displayed in 3 columns. Unique to file1, Unique to file2 and the same in both files.
  - Both files **has to be sorted**

**cmp**
  - Compares two files, byte by byte and return the positions of the first difference
  - Only first difference is returned

<br>

## ## Use input-output redirection (e.g. >,>>,|,2>)

**File descriptors**
  - `stdin (0)`
    - Standard input.
    - How data is entered or presented for processing.
    - Typically the keyboard or mouse. But could also be a file.
  - `stdout (1)`
    - Standard output.
    - The data returned from a command.
  - `stderr (2)`
    - Standard error.
    - Error messages that are returned, kept separate from stdout.

**Redirect Operators**
  - `| (pipe)`
    - The pipe command is used to send output from one command to another command for processing.
    - `$ cat /var/log/syslog | less`
  - `>`
    - Redirect stdout.
    - Used to write command output to a file
    - Will create a file if it doesn't exist or will overwrite an existing file.
    - `$ ls -l /etc > etc.txt` Will list `/etc` directory and redirect the output to `etc.txt` file (will overwrite the content).
  - `>>`
    - Redirect stdout.
    - Used to append command outout to a file.
    - Will create a file if it doesn't exist or add output to an existing file
    - `$ ls -l /etc >> etc.txt` Will list `/etc` directory and redirect the output to `etc.txt` file (will append (won't remove existing content)).
  - `<`
    - Redirect stdin
    - Used to direct the content of a file to a command
    - Often used to send data to a script for processing but works with commands too.
    - `less < etc.txt`
  - `2>`
    - Redirect stderr
    - `find /proc -name "cpu*" 2> /dev/null` If running as non-root user you will probably receive `Permission Denied` in this case this error will be redirected to `/dev/null` (virtual file that discard all data) and won't be visible in the screen.
  - `2>&1`
    - Redirect stderr to stdout.

<br>

## ## Analyze text using basic regular expressions.
Regular expression, often referred to as regex or regexp, is a specific series of characters which are used to define a search pattern.
A regex is most often used for "find all" or "find and replace" activity.
It is also used when you only know a part of a string or if you're using a wildcard search.

Regex basics:
  - `^` the start of a string or line.
  - `$` the end of a string or line.
  - `.` wildcard which can match any character, except newline.
  - `?` every single character
  - `|` matches a specific character or group of characters on either side (`a|b` corresponds to a or b)
  - `\` used to escape a special character.
  - `t` the character t
  - `az` the string az
  - `[ab]` the list of characters to be matched

Regular Expressions with examples

| Character |                Definition                |  Example   |        Result         |
| :-------: | :--------------------------------------: | :--------: | :-------------------: |
|     ^     |            Start of a string             |    ^abc    |    abc, abcd, abc1    |
|     $     |             End of a string              |    abc$    |  abc, rasabc, 2aabc   |
|     .     |       Any character except newline       |    a.c     |     abc, acc, a1c     |
|           |                                          | Alteration |           a           |
|   {...}   | Explicit quantity of preceding character |   ab{2}c   |         abbc          |
|   [...]   |   Explicit set of characters to match    |   a[bB]c   |        abc,aBc        |
| [a-z0-9]  |   One lower case characters or number    | a[a-z0-9]c |        aac,a1c        |
|   (...)   |           Group of characters            |  (abc){2}  |        abcabc         |
|     *     | Null or more of the preceding characters |    a*bc    | bc, abc, aabc, aaaabc |
|     +     |  One or more of the preceding character  |    a+bc    |       abc, aabc       |
|     ?     |  Null or one of the preceding character  |    a?bc    |        bc, abc        |
|    ^$     |               Empty string               |            |                       |

* Not all regular expressions are supported by `grep`. As alternative can be used `egrep`

Examples:

List all files/directories that begin with a
  - `$ ls -l a*`

List all files/directories formed by two characters that begin with a
  - `$ ls -l a?`

List files/directories called aa or ab
  - `$ ls -l a[ab]`

List files/directories called aa, ab and ac
  - `$ ls -l a[a-c]`

Search for pattern inside files in `path/*`. Only file name is showned.
  - `$ grep -l pattern path/*`

Search for pattern inside files in `path/*` and path subdirectories as well.
  - `$ grep -lr pattern path/*`

Search pattern ignoring case inside file in path/* and path subdirectories.
  - `$ grep -lri pattern path/*`

Search for every line that starts with "The"
  - `$ grep '^The' filename`

Search for every line that starts with letter T and doesn't end with E
  - `$ grep '^T[a-z][^e]' filename`

Search for every instance of "the" or "The"
  - `$ grep '\<[Tt]he\>' filename`

Search for email address
  - `$ grep E -o "\b[A-Za-z0-9.+%-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,6}\b"`

<br>

Examples with `sed` (without `-i` option the results are not saved)

In any row of file it will change first occurence of true to false.
  - `$ sed 's/true/false' filename`

Does the same as above just for all occurences instead of the first
  - `$ sed 's/true/false/g' filename`

Does the same as above mentioned just case **insensitive**
  - `$ sed 's/true/false/gI' filename`

Change first occurrence of true to false but only for row 10
  - `$ sed '10s/true/false' filename`

Change first occurence of true to false in any row of file
  - `$ sed -n 's/true/false/p' filename`

Print rows that contain true
  - `$ sed -n '/true/p' filename`

Print rows from 2 to 4
  - `$ sed -n 2,4p filename`

Delete rows with true
  - `$ sed '/true/d' filename`

Delete row 12
  - `$ sed -n 12d filename`

Insert `cool text` as line 11
  - `$ sed '11cool text' filename`

Change true to false in all occurrence and save it to file
  - `$ sed -i 's/true/false/g' filename`

Change true to false in all occurrence and save it to file but also keep a copy of original file
  - `$ sed -i.orign 's/true/false/g' filename`

<br>

## ## Archive, Backup, Compress, Unpack and Decompress files.
TAR (Tape ARchive) is a Linux application that was originally created to write files to a tape drive. Now commonly used for creating archive files to be used for backup and recovery by collecting a series of files and directories into a single file.


Create an tar file (or tarball)
  - `$ tar cvf filename.tar /path/to/source`

Create a compressed tar file using gzip
  - `$ tar cvfz filename.tar /path/to/source`

Extract contents of a tar file using gzip
  - `$ tar xvfz filename.tar.gz`

Create a compressed tar file using bzip2
  - `$ tar jcfv filename.tar.bz2 /path/to/source`

Extract contents of a tarfile using bzip2
  - `$ tar jxfv filename.tar.bz2`

View contents of a tar file
  - `$ tar -rf filename.tar.gz`

Delete a file from tar
  - `$ tar --delete -f filename.tar file`

Update file in tar
  - `$ tar --update -f filename.tar file`

Create a backup of device
  - `$ dd if=/dev/sda of=sda.img`

Restore backup
  - `$ dd if=sda/img of=/dev/sda`

**rsync**
  - It is used to keep synchronized the content of two diretories.
  - `$ rsync -av source destination` Synchronize source with destination.
    - `$ rsync -avz /tmp username@192.168.1.100:/destination` Synchronize tmp with destination (on remote machine with ip 192.168.1.100). `-z` means that the content will be compressed during transfer.
  - `$ rsync -avzhe ssh source root@remote_host:/remote_directory/` Synchronize source with remote_directory using ssh.

<br>

## ## Create, delete, copy and move files and directories
Create a file
  - `$ touch filename`
  - `$ nano filename` (need to save)
  - `$ echo text > filename`

Create a directory
  - `$ mkdir something` Will create directory named something
  - `$ mkdir -p /etc/test/something` Creates all directories that are not created in provided path.

Copy a file/directory
  - `$ cp src_filename dst_filename`
  - `$ cp filename1 filename2 /etc` Copy two files to `/etc` directory
  - `$ cp -r dir1 dir2` Copy dir1 to dir2 (recursive).

Move file or directory
  - `$ mv src_filename dst_filename` (or directory name)

Rename a file or directory
  - `$ mv old_filename new_filename` Yes. The same mv command is used for this.

Delete a file
  - `$ rm filename`

Delete a directory
  - `$ rmdir directory` works only if directory is empty.
  - `$ rm -r directory` deletes all files along with the directory
  - `$ rm -rf directory` delete all files and directories without warning.

<br>

## ## Create and manage hard and soft links
`inode` is a data structure in a Unix-style file system that describe a file-system object (such as file or directory). 
Obejct attributes includes metadata (time of last change, access, modification) as well as owner and permission data.
Directories / files are just named that are assigned to inodes. 
Each inode is identified by a unique number.

**Hard link**
  - Direct pointer to a file
  - Can only be a file
  - Shares the same inode as source
  - Must be on the same filesystem
  - As long as hard link exists the data exists.
  - `ln target new_name` Create a hard link to target with new name `new_name`

**Soft Link**
  - A redirect to a file (think of a shortcut or alias)
  - Can be a file or directory
  - Has a unique inode
  - Can be on a different filesystem or mounted share
  - If the source file is deleted, the soft link is broken.
  - `ln -s target new_name` Create a symbolic link to target called `new_name`
  - `ln -s /etc .` Create a symbolic link to etc directory in current directory. Will be named the same as `etc`.

To check inode number:
  - `$ ln -li`

<br>

## ## List, set and change standard file permissions

Check current file or directory permissions
  - `$ ls -l`
  - `$ ls -l filename`

Permissions are set owner-group-others
  - `rw-r--r--   1   root   root   399 B     Mon Jul 26 15:12:04 2021  Gemfile`
  - Read and write for owner, read for group, read for others.
  - In octal it would look like: `644`

Octals values
  - Read
    - Octal value: 4
  - Write
    - Octal value: 2
  - Execute
    - Octal value: 1

Read/Write/Execute meaning depends wheter it's file or directory we are talking about:

|           |     File     |   Directory   |
| :-------: | :----------: | :-----------: |
| Read (4)  | Read or Exec |   List (ls)   |
| Write (2) |    Modify    | Create Delete |
| Exec (1)  |     Run      |      cd       |

**Grant full access to everyone**
  - `$ chmod 777 filename`
  - `$ chmod u+rwx filename`
    - `$ chmod g+rwx filename`
    - `$ chmod o+rwx filename`
    - As you see in relative mode you have to repeat the same commands for each mod (owner, group, other)

**Remove execute access grom group and other**
  - `$ chmod 766 filename`
  - `$ chmod u-x filename` and `$ chmod o-x filename`
    - `-` sign means remove, `+` sign means add permission.

**Advanced (special) permissions**
|                |         File         |                          Directory                          |
| :------------: | :------------------: | :---------------------------------------------------------: |
|    suid (4)    | Run as owner of file |                             N/A                             |
|    sgid (2)    |  Run as group owner  |       Inherit directory group when a file is created        |
| sticky bit (1) |         N/A          | A file can be deleted only by owner or by directory's owner |

**suid (Set owner User ID upon execution)** - when a file with setuid is executed it will ran with the permissions of a file owner instead of the user that is trying to execute it. This enables users to be treated temporarily as root (or another user). As example `passwd` has such bit set.
  - setuid is ignored on all interpreted executables (i.e.e executables starting with `#!`)

**sgid (Set Group ID upon execution)** - same as `suid` just in this case file is executed with the permissions of a file group instead of the user that is trying to execute it. If it is set on folder all new files created there will inherit the group that is set on current folder.

**Sticky bit** - when sticky bit is set on file or directory - it can be deleted only by the owner. It can be useful when you give W permission to everyone and you want them to be able to edit the file, but to protect the file from being deleted by someone else than you (by default you can delete the file if you have write permissions)

Exapmles how to set special permissions:
Absolute mode:
  - `$ chmod 4760 filename`
    - `4` will add suid permission to the file
    - `7` will add read, write, execute permissions to the owner.
    - `6` will add read, write permissions to the group
    - `0` will remove all permissions from others.

Relative mode:
  - `$ chmod u+s filename` set suid
  - `$ chmod g+s filename` set guid
  - `$ chmod o+s filename` set sticky bit.

Modify ownership examples:

Change File/Directory owner
  - `$ chown root:linux filename` filename will be owned by root user and linux group
  - `$ chown root filename` Just the file owner will be changed to root
  - `$ chown :linux filename` Just the group will be changed to linux

<br>

## ## Read and use system documentation
Show short help of a specific command
  - `$ command --help`
  - `$ timedatectl --help`
  - `$ ip --help`

Show full manual for specific command
  - `$ man command`
  - `$ man timedatectl`
  - `$ man ip`

Show all possible manuals by keyword
  - `$ man -k time`

Directory that usually contains configuration files examples and etc
  - `/usr/share/doc`

Show info documents for specific command
  - `$ info yum`

<br>

## ## Manage access to the root account
root user is the system administrator
  - When logged as root shell prompts `#`, otherwise it will be `$`

Becoming a root user
  - `$ sudo -i`

Becoming a another user
  - `$ su - username`
  - `$ su - devops`

Run a command with root privileges
  - `$ sudo ip route add default via 192.168.1.254`
  - `$ sudo cat /etc/sudoers`

By default group `wheel` is authorized to act as root (use sudo). To add user to `wheel group` use:
  - `$ usermod -aG wheel username`

To edit sudoers file (add permissions to who can use sudo command)
  - `$ visudo`

`visudo` basic configuration example
  - `demo ALL=(ALL:ALL) ALL`
    - The first field `demo` indicates the username that the rule will apply to
    - The second field `ALL` indicates that this rule applies to all hosts
    - The third field `ALL` indicates that the user demo can run commands as all users
    - The forth field `ALL` indicates that the user demo can run commands as all groups.
    - The fifth (last) field `ALL` indicates these rules apply to all comands.

in `visudo` configuration adding `%` before first field indicates that it will apply to a group.
  - `%develoeprs ALL=(ALL:ALL) ALL`
  - `%developers localhost=/sbin/shutdown -h now` users in developer groups will be able to run `shutdown -h now` command on localhost as root.
