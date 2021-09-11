# Chapter 2. Operation of Running Systems.

## ## Boot, reboot, ant shutdown a system safely.

`shutdown` is the go-to command for shutting down, powering off or rebooting a machine. It works on System 5 and Systemd based operating systems.

Reboot the server at the time specified (20:00)
  - `$ shutdown -r 20:00`

Reboot the server in 20 minutes
  - `$ shutdown -r +20`

Reboot the server now
  - `$ shutdown -r now`

Halt the server (halts all CPU activities)
  - `$ shutdown -h now`

Shutdown the server
  - `$ shutdown -P`

Alternative to `shutdown -r`
  - `$ reboot`

Alternative to `shutdown -h`
  - `$ halt`

Alternative to `shutdown -P`
  - `$ poweroff`

<br>

## ## Boot or change system into different operating modes

**Operating modes**:
  - System halt
    - SysVinit: 0
    - Systemd: runlevel0.target, poweroff.target
  - Single user mode
    - SysVinit: 1
    - Systemd: runlevel1.target, rescue.target
  - Multi-user
    - SysVinit: 2
    - Systemd: runlevel2.target, multi-user.target
  - Multi-user with network
    - SysVinit: 3
    - Systemd: runlevel3.target, multi-user.target
  - Experimental
    - SysVinit: 4
    - Systemd: runlevel4.target, multi-user-target
  - Multi-user with network and graphical mode
    - SysVinit: 5
    - Systemd: runlevel5.target, graphical.target
  - Reboot
    - SysVinit: 6
    - Systemd: runlevel6.target, reboot.target

Check the current `runlevel`:
  - `$ systemctl get-default`

Show all available targets:
  - `$ systemctl list-units --type target --all`

Set multi-user target as default
  - `$ systemctl set-default multi-user.target`

**Tutorial on how to change operating mode at boot time:**
  - During boot press ESC to prompt the `grub2` menu
  - Highlight item that you want makes changes to and press `e`
  - Locate the line that starts with `linux16` and at the end of it add which mode you want to boot in.
    - Example, to boot in emergency mode at the end of the line you would add `systemd.unit=emergency.target`
    - NOTE: When booted in this mode disk is mounted read only, to mount it to be able to read/write use `mount -o remount,rw /`
  - After making changes press `ctrl+x` to apply it and boot.

<br>

## ## Install, configure and troubleshoot bootloaders
Linux boot process:
1. A process known as `POST` (Power-On Self Test) performs an overall check on the hardware components of your computer.
2. When `POST` completes, it passes the control over to bootloader, which in turn loads the Linux kernel in memory (along with `initramfs`) and executes it. The most used bootloader in Linux is the GRUB.
3. The kernel checks and accesses the hardware and then runs the initial process (mostly known by its generic name `init`) which in turn completes the system boot by starting sevices.

GRUB2 (**GR**and **U**nified **B**ootloader **2**) is the bootloader installed on most common distributions available. It's the default bootloader (centos, ubuntu, etc.).

Grub configuration files is located in `/boot/grub2/grub.cfg` on Centos/RHEL distros or in `/boot/grub/grub.cfg` on Debian/Ubuntu distributions. Menu scripts can be found in `/etc/grub.d/` directory. **These CFG files are NOT to be edited by hand, they're based on the contents of `/etc/default/grub` and the files found inside `/etc/grub.d/`.**

GRUB Bootloader allows users to:
  - Modify the way the system behaves by specifying different kernels to use.
  - Choose between alternate operating systems to boot
  - Add or edit configuration stanzas to change boot options, among other things.


Open grub documentation
  - `$ info grub2`

Open grub documentation specifically about `/etc/default/grub` file
  - `$ info -f grub2 -n 'Simple configuration'`

Default grub configuration file with explanations:
```
GRUB_TIMEOUT=1 <--- Boot the default entry this many seconds after the menu is displayed, unless a key is pressed. Set to 0 to boot immediately, without displaying the menu, or to -1 to wait indefinetely.

GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)" <--- Set by distributors of GRUB to their identifying name. THis is used to generate more informative menu entry titles.

GRUB_DEFAULT=saved <--- Default menu entry. This can be a number or the title of a menu entry , or the speial string 'saved'.

GRUB_DISABLE_SUBMENU=true <--- Disables submetnu

GRUB_TERMINAL="serial console" <--- If this option is set, it overrides both GRUB_TERMINAL_INPUT and GRUB_TERMINAL_OUTPUT to the same value.

GRUB_SERIAL_COMMAND="serial --speed=115200" <--- A command to configure the serial port when using the serial console.

GRUB_CMDLINE_LINUX="console=ttyS0,115200 console=tty0 vconsole.font=latarcyrheb-sun16 crashkernel=auto  vconsole.keymap=us" <--- Command-line arguments to add to menu entries for the Linux kernel.

GRUB_DISABLE_RECOVERY="true" <--- If this option is set to true it disables the generation of recovery mode menu entries
```

To change bootloader configuration edit `/etc/default/grub` file
  - `$ vi /etc/default/grub`

After making changes grub.cfg needs to be regenerated
  - `$ grub2-mkconfig -o /boot/grub2/grub.cfg`

Print all available BOOT options:
  - `$ awk -F\' '$1=="menuentry " {print $2}' /boot/grub2/grub.cfg`

To configure GRUB2 to boot in `single-user mode` append word `single` to `GRUB_CMDLINE_LINUX` in `/etc/default/grub`
  - `GRUB_CMDLINE_LINUX="vconsole.keymap=la-latin1 rd.lvm.lv=centos_centos7-2/swap crashkernel=auto  vconsole.font=latarcyrheb-sun16 rd.lvm.lv=centos_centos7-2/root rhgb quiet single"`

After any changes do not forget to regenerate grub.cfg file.

To install grub on X partition:
  - `$ grub2-install /dev/sdX`

<br>

## ## Diagnose and manage processes
Linux has multiple tools available to help to monitor process behaviours and performance. Some of them are:

**ps**
  - Displays a snapshot of processes at a specific point in time
  - Useful to capture running processes, check process ownership and process PIDs
  - command usage examples:
    - `$ ps ef` displays USER, UID, PID, PPID, Start Time, TIME, CMD
    - `$ ps aux` displays USER, PID, CPU, MEM, VSZ, RSS, STAT, Start Time, Time, CMD
    - `$ ps -eo pid,ppid,cmd,%cpu,mem --sort=-%cpu` Will show selected columns and sort by `%cpu` column.
    - `$ ps -e -o pid,args --forest` will display output in tree format

By having process id you can navigate in `/proc` directory.
  - `$ cd /proc/5423`

View all files and process has opened, named by its file descriptor (it's a symbolic link to a actual file).
  - `$ cd /proc/5423/fd`

List all open files associated with specific process id
  - `$ lsof -p 5423`

**top**
  - Displays Linux processes
  - Provides Dynamic, real-time view of a running operating system

**htop**
  - Extends on top's abilities
  - Offers color highlighting
  - Mouse integration
  - The ability to search, filter, sort and kill processes

**Background processes**

Execute command and run it in background
  - `$ sleep 600 &` - & instructs to run it in background.

List processes running in background
  - `$ jobs`

Return process in foreground
  - `$ fg pid`


**Process priority (nice and renice)**

**nice** - Modifies the priority for a new process (-20 is the highest and 19 is the lowest).

**renice** - Modifies the priority of a running process.

Show process `nice` value
  - `$ ps -e -o pid,nice,command`

Run a command in background with a specific nice value
  - `$ nice -n -10 command &`

Change nice value to 5 for a running process
  - `$ renice -n 5 pid`

**Signals**

Send a SIGTERM to process
  - `$ kill pid`

Send a SIGKILL to process
  - `$ kill -9 pid`

List all available signal and corresponding number:
  - `$ kill -l`

<br>

## ## Locate and analyze system log files
Core system log files are found in `/var/log` (Debian/RedHat based systems). However, the naming can differ

**Debian / Ubuntu**
  - `/var/log/syslog` - system information
  - `/var/log/auth.log` - account authentication information

**RedHat / CentOS**
  - `/var/log/message` - system information
  - `/var/log/secure` - account authentication information.

In CentOS many tools use rsyslog to manage logs. `rsyslog` is a daemon that permits the logging of data from different types of systems in a central repository.
  - `/etc/rsyslog.conf` - configuration file
  - `sytemctl status rsyslog` - check status of service.

<br>

## ## Schedule tasks to run at a set date and time
The **cron** daemon uses entries in the crontab file to execute recurring activities like clearing directories, creating backups, generating reports or updating configuration.

cron executes tasks (called jobs) to run at specific time.

`/etc/crontab`
  - normally isn't edited
  - contains a template for cron tasks, so can be used as a reminder.

`/etc/cron.d`
  - It contais files with the same syntax as `/etc/crontab`
  - Normally is used by software packages installed on the system.

`/etc/cron.hourly`
  - Each script in this directory will be executed every hour
  - Don't need to specify time when to execute.

`/etc/cron.daily`
  - Each script in this directory will be executed every day
  - Don't need to specify time when to execute.

`/etc/cron.weekly`
  - Each script in this directory will be executed every week
  - Don't need to specify time when to execute.

`/etc/cron.monthly`
  - Each script in this directory will be executed every month
  - Don't need to specify time when to execute.

`/var/spool/cron`
  - All tasks/jobs created via `crontab` are stored in this directory

To manage (add/delete/update) cron tasks with custom time for current user:
  - `$ crontab -e`

To manage other users cron tasks (used by root)
  - `$ crontab -e -u username`

List all crontab jobs under the current user
  - `$ crontab -l`

List all crontab jobs for specific user
  - `$ crontab -l -u username`

Crontab syntax when adding via `crontab -e`:
```
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
# │ │ │ │ │                                   7 is also Sunday on some systems)
# │ │ │ │ │
# │ │ │ │ │
# * * * * * command to execute
```

`#` - is a comment

`*` - any/all/always

`1 0 * * * echo testing` - command will be executed every day at 00:001

`1-30 * * * * echo testing` - command will be executed every day, every hour at minutes from 1 to 30.

`*/10 * * * * echo testing` - command will be executed every ten minutes

`00 */2 15 * * echo testing` - command will be executed every two hours on 15th day of every month.

`00 1-9/2 1 5 * echo testing` - command will be executed on 1st May at 1:00, 3:00, 5:00, 7:00, 9:00 (every two hours from 1 to 9)

`00 13 2,8,14 * * echo testing` - command will be executed on second, eighth, fourteenth day of every month at 13:00.

<br>

## ## Verify completion of scheduled jobs
Information about completed scheduled jobs can be written in several locations which may vary between distributions. Jobs might also be written to custom logs if it's defined like so in the script/job.

**Debian**
  - `/var/log/syslog` (grep for `cron`)

**CentOS**
  - `/var/log/cron`

Also log directory can be checked/changed in `/etc/rsyslog.conf`file:
```
# Log cron stuff
cron.*                                                  /var/log/cron
```

<br>

## ## Update software to provide required functionality and security
**Debian based systems**

**dpkg**
  - Package manager for Debian based systems
  - Low-level tool used to install, remove and provide information for downloaded `.deb`packages
  - Does not perform dependency checks

**dpkg examples:**
  - `$ dpkg -i filename.deb` install deb package
  - `$ dpkg -l` list all installed packages
  - `$ dpkg -l package_name` list specified package
  - `$ dpkg -S package_name` identify source for file/application
  - `$ dpkg -s package_name` show all information about package
  - `$ dpkg -r package_name` remove package

**aptitude**
  - High-level interface to the Debian package system
  - Provides a command-line frontend interface to install, upgrade, remove and provide information for .deb packages
  - Manage dependency checks
  - Can be used with command-line options but more commonly used for its interface

**aptitude examples:**
  - `aptitude install pkgname` - install
  - `aptitude show pkgname` - list info about package
  - `aptitude remove pkgname` - remove

**apt / apt-get**
  - High-level interface to the Debian packages system
  - Provides a command-line interface to install, upgrade, remove and provide information for .deb packages
  - Manage dependency checks

**apt / apt-get examples:**
  - `apt install pkgname` - install
  - `apt-cache show pkgname` - list info about a package
  - `apt purge pkgname` - remove package and configuration
  - `apt update` - refreshes repository index
  - `apt upgrade `- upgrades all upgradeable
  - `apt autoremove` - removes unwanted packages
  - `apt full-upgrade (apt-get dist-upgrade)` - upgrades packages with auto-handling of dependencies
  - `apt search (apt-cache search)` - searches for package
  - `apt show (apt-cache show)` - show packages details

**RedHat Based Systems**

**rpm**
  - Package manager for RedHat based systems (CentOS)
  - Low-level tool used to install, remove and provide information for downloaded rpm packages
  - Manually lists packages dependencies
  - Performs dependency checks but does not automatically install

**rpm examples:**
  - `rpm -ivh filename.rpm` - install package
  - `rpm -Uvh filename.rpm` - upgrade package
  - `rpm -e pkgname` - remove package
  - `rpm -qa` - list installed packages
  - `rpm -qc pkgname` - list all configuration files of that package
  - `rpm -qp pkgname` - query package
  - `rpm -qp --scripts pkgname` - show what will be executed after installing package
  - `rpm -qi pkgname` - get package info-
  - `rpm -qf /path/to/file` - Tell what RPM packages does this file belong to.

**yum**
  - Package manager for RedHat systems
  - Official high-level tool that provides a command-line interface to install, upgrade, remove and provide information about .rpm packages
  - Manages dependency checks

**yum examples:**
  - `yum install pkgname` - install
  - `yum remove pkgname` - remove
  - `yum update pkgname` - update package
  - `yum list installed` - list installed packages
  - `yum search keyword` - search repositories for a package

**dnf**
  - High-Level tool that provides a command-line interface to install, upgrade, remove and provide information about .rpm packages
  - Manages dependency checks
  - Default package manager for Fedora and Redhat 9. Designed for better performance, memory optimization and improved dependency resolution
  - Shares many commands and similarities with yum

**dnf examples:**
  - `dnf install pkgname` - install
  - `dnf remove pkgname` - remove
  - `dnf update pkgname` - update package
  - `dnf list installed` - list installed packages
  - `dnf search keyword` - search repositories for a package

<br>

## ## Verify the integrity and availability of resources
System resources are components within a computer system and includes memory (RAM), Hard Disks, CPU and network IO (Inout/Output).

**Memory (RAM)**
  - Run Memory test routine (requires reboot)
    - Reboot into `GRUB` menu
    - Select the Memory test entry to run the tests

**Hard Disk**
  - Unmount the problematic filesystem
    - Never check a mounted filesystem
    - `umount /path/to/fs`
  - Use `fsck` utility to check the filesystem and drive
    - `fsck /path/to/fs``
  - Use `tune2fs` utility to schedule a check at reboot
    - `tune2fs -c 1 /path/to/fs`

**Verify the integrity of rpm database**
  - `/usr/lib/rpm/rpmdb_verify /var/lib/rpm/Packages`

<br>

## ## Verify the integrity and availability of key processes
Processes are aplications that are actively loaded and/or running in an operating system. These processes consume memory and CPU cycles, and can occasionally have issues.
There is a couple of tools that can help to monitor ant manage processes behaviour.

**ps (process status)** - displays information about active processes

Examples:
  - `$ ps -e`
  - `$ ps -ef`
  - `$ ps aux`
  - `$ ps aux --forest (parent process in tree format)`

Can be combined with other commands like grep to filter output even more:
  - `$ ps -ef | grep cron`
  - `$ ps aux | grep -v grep | grep -i -e cron`

**top** - displays process activity in realtime, simimlar to Task Manager on Windows.

**htop** - Similar to top, but is interactive and has more options available (usually not installed by default on most distributions).

<br>

## ## Change kernel runtime parameters, persistent and non-persistent
Linux admin may find a situation where they will need to change certain kernel parameters without shutting down or restarting the system. These changes can be completed in a persistent or non-persistent fashion, depending on requirements.

Kernel parameters are used to customize the behavior of the system.

After making changes run `sysctl -p` to load the new configuration.

Non-persistent changes are temporary and will be reset once the system is rebooted.

  - `$ sysctl -a` view all kernel parameters
  - `$ sysctl dev.cdrom.autoclose` view specific kernel parameter
  - `$ sysctl -w dev.cdrom.autoclose=0` update the specific kernel parameter value

You can also edit configuration files:
  - `$ echo 0 > /proc/sys/dev/cdrom/autoclose`
  - `$ vi /proc/sys/dev/cdrom/autoclose`

Persistent changes are permanent and will remain once the system is rebooted. After the changes have been made run `sysctl -p /etc/sysctl.d/99_my_sysctls.conf` to load the new configuration file or `sysctl --system` to load all configuration files

  - `$ vi /etc/sysctl.d/10-network-security.conf` open to edit configuration file
  - `net.ipv4.ip_forward=1` add the following line in the configuration file

After making changes execute one of the following to apply changes:
  - `$ sysctl --system`
  - `$ sysctl /path/to/changed/config`

<br>

## ## Use scripting to automate system maintenance tasks
Shell scripting is a valuable skill for a Linux administrator. A good script can save time, provide consistency and reduce the potential for a errors in repetitive processes.

Example bash script:
```
#!/bin/bash <-- shebang (what interpreter to use)
echo
echo "This is a basic bash script example"
echo
echo "User: "`whoami`
echo "Current Directory: "$( pwd )
# echo -n "Current shell:"
lsb_release -d
echo
```

**Operators and IF**

Operators are used to evaluate, combine or perform an action against information (operands) provided. There are several types of operators (including boolean, string and file tests):
  - Arithmetic operators performs traditional math functions such as addition, substraction, multiplication, division and modulus.
    - 2 + 2
    - 4 - 2
    - 4 * 2
    - 4 / 2
    - 4 % 3
  - Relational operators are used to compare the relantionship between numeric operands and return `true` or `false`
    - -eq (equal)
    - -ne (not equal)
    - -gt (greater than)
    - -lt (less than)
    - -ge (greater than or equal to)
    - -le (less than or equal to)


`if` statement is one of the most basic and common comparative function used in scripting.
```
if [ true = true ]; then
  echo Good
else
  echo Bad
fi
```

Example script:
```
#!/bin/bash
read -p "Enter A value: " a
read -p "Enter B value: " b

echo $A is equal to $B?
if [ $A -eq $B ]; then
  echo "$A is equal to $B"
else
  echo "$A is not equal to $B"
fi
```

Another example:
```
#!/bin/bash
read -p "Enter a directory path to search for: " DIRECTORY
if [ -d $DIRECTORY ]; then
  echo "The directory ($DIRECTORY) exists."
else
  echo "The directory ($DIRECTORY) does not exist
fi
```

And one more:
```
#!/bin/bash
echo
read -p "Enter a number between 1 and 100: " number
echo
if [ $number -ge 75 ]; then
  echo $number "is greater than or equal to 75."
elif [ $number -ge 50 ]; then
  echo $number "is greater than or equal to 50."
else
  echo $number "is less than 50."
fi
echo
```

**Loops - for/while/until**
`for` loop will repeat a specific number of times, either determined by the script or by user input. This loop counts the number of times the content will be evaluated or executed.

Examples:
```
#!/bin/bash
for TIMER in 0 1 2 3 4 5
do
  echo "Count Up: " $TIMER
done

\\\\\\\\\\\\

#!/bin/bash
for url in $(cat listofurls.txt)
do
  curl "$url" >> webpage_output_combined.html
done
```

`while` loop will repeat if the condition evaluated is `true`. If the evaluation fails on the first check the loop will never start.

Example:
```
#!/bin/bash
while read url
do
  curl "$url" >> webpage_output_combined.html
done < listofurls.txt
```

`until` loop will repeat if the condition evaluated is `false`. If the evaluation fails on the first check, the loop will never start.

Example:
```
#!/bin/bash
TIMER=0
until [ $TIMER -gt 5 ]
do
  echo Count Up: $TIMER
  ((TIMER++))
done
```

Example of script  that will increase file size until 1KB:
```
# increase file until 1KB
#!/bin/bash
FILENAME=`basename "$0"`
echo $FILENAME
TMP_FILE="./tmp1"
TARGET_FILE="./target"
cat $FILENAME > $TARGET_FILE
FILESIZE=0

until [ $FILESIZE -gt 1024 ]
do
  cp $TARGET_FILE $TMP_FILE
  cat $TMP_FILE >> $TARGET_FILE
  FILESIZE=`du $TARGET_FILE | awk '{print $1}'`
  echo "Filesize: $FILESIZE"
  sleep 1
done
```

<br>

## ## Manage the startup process and services (In Services Configuration)
Linux service is any application or set of applications configured to launch at system startup and run in the background. On modern Linux distributions services are managed by `systemctl`.

Check service status
 - `$ systemctl status <service>`

Start / Stop / Restart service:
 - `$ systemctl start <service>`
 - `$ systemctl stop <service>`
 - `$ systemctl restart <service>`

Enable / Disable service:
 - `$ systemctl enable <service>`
 - `$ systemctl disable <service>`

Check if service is enabled
 - `$ systemctl is-enabled <service>`

List all systemd unit objects available
 - `$ systemctl list-unit-files`

<br>

## ## List and identify SELinux/AppArmor file and process contexts
In computer security, mandatory access control (MAC) refers to a type of access control by which the operating system constrains the ability of a subject or initiator to access or generally perform some sort of operation on an object or target. In practice, a subject is usually a process or thread; objects are constructs such as files, directories, TCP/UDP ports, shared memory segments, IO devices, etc. Subjects and objects each have a set of security attributes. Whenever a subject attempts to access an object, an authorization rule enforced by the operating system kernel examines these security attributes and decides whether the access can take place. Any operation by any subject on any object is tested against the set of authorization rules (aka policy) to determine if the operation is allowed.

**SELinux**
  - Stands for Security Enchanced Linux
  - Is a kernel module for supporting access control security policies, including mandatory access controls.
  - Is has been part of the RedHat/CentOS ecosystem since 2005.

Commands:
 - `$ semanage fcontext -l` show all selinux contexts
 - `$ ls -Z` list all files and show their context
 - `$ ps auxZ` list all processes and show their context
 - `$ getenforce` check the current SELinux mode
 - `$ setenforce` update SELinux mode

<br>

## ## Manage Software
**yum**

Find packages by some sort of a string. By default search will try searching just package names and summaries, but if it fails it will then search the descriptions and url.
 - `$ yum search keyword`

Show information about specific package
 - `$ yum info package_name`

Install package
 - `$ yum install package_name`

Search for package that contain file
 - `$ yum provides */file`

Remove specific package
 - `$ yum remove package_name`

Remove specific package + unused dependencies
 - `$ yum autoremove package_name`

Download RPM package (`yum-utils` has to be installed)
 - `$ yumdownloader package`

**RPM**
Install `file.rpm`
 - `$ rpm -i file.rpm`

Upgrade file.rpm
 - `$ rpm -U file.rpm`

List all installed RPMs
 - `$ rpm -qa`

Tells to what RPM package belongs
 - `$ rpm -qf file`

<br>

## ## Identify the component of a Linux distribution that a file belongs to
Search for a package that contains file
 - `$ yum provides */file`

Show all libraries used by command
 - `$ ldd /path/to/command`

Rebuild library cache
 - `$ ldconfig`
