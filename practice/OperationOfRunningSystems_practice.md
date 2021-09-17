# Chapter 2. Operation of Running Systems.

## ## Boot, reboot, ant shutdown a system safely.
1. Schedule a shutdown of the server at 23:15
2. Schedule a restart of the server in 30 minutes
3. Restart the server without delay
4. Shutdown the server without delay
5. Halt the system
6. Schedule a system HALT in 45 minutes
7. Cancel the HALT schedule.

<br>

## ## Boot or change system into different operating modes
1. Check what is the current `runlevel`
2. Check what are all available targets
3. Change default `runlevel` to rescue.target
4. Reboot the server and enter to `emergency` mode via GRUB2.
5. Reset root password (without login to the system)
<br>

## ## Install, configure and troubleshoot bootloaders
1. Review grub documentation
2. Open `Simple configuration` page in documentation
3. Increase GRUB menu timeout to 10 seconds and regenerate the configuration
4. Configure GRUB to BOOT in `multi-user` mode
5. Print all available GRUB menu entries

<br>

## ## Diagnose and manage processes
1. Display currently running processes
2. Display currently running processes with only pid,user and command columns
3. Display currently running processes in a tree layout.
4. Take random process id and check what files that process has opened.
5. Identify which process uses the most of CPU time
6. Identify which process uses the most RAM
7. List all open files associated with 5 and 6 results.
8. Run `dd` command in the background
9. List all processes running in a background
10. Return process to foreground.
11. Start new process with nice value of 7
12. Change any running process nice value to -10
13. List all processes and check nice value
14. Kill a process with SIGTERM
15. Kill a process with SIGKILL
16. List all available kill signals.
17. Install `htop`
18. Display all `bash` processes
<br>

## ## Locate and analyze system log files
1. Investigate log file which contains system information
2. Investigate log file which contains account authentication information
3. Investigate log file which contains information about cron tasks

<br>

## ## Schedule tasks to run at a set date and time
1. Create a script that will be executed every hour
2. Create a script that will be executed every week
3. Create a cronjob that will run every minute
4. Create a cronjob that will run every minute at 13:00
5. Create a cronjob that will run once a day from Monday to Friday
6. Create a cronjob that will run on second and third day of May
7. Create a cronjob that will run every two hours on 15th day of every month
8. Create a cronjob that will run only on weekends
9. Create a cronjob that will run from monday to friday on July every hour.
10. Create a crontajob that will run only on Thursday at 17:00

<br>

## ## Verify completion of scheduled jobs
1. Verify if above created crons runs as expected.

<br>

## ## Update software to provide required functionality and security
1. Download and install https://distrib-coffee.ipsl.jussieu.fr/pub/linux/altlinux/autoimports/Sisyphus/noarch/RPMS.autoimports/beakerlib-1.18-alt1_8.noarch.rpm
2. Remove beakerlib package
3. List all installed packages (with `rpm` and `yum`)
4. Show all configuration files of `dnsmasq` package (install if not available)
5. Query package `dnsmasq`
6. Check what scripts will be executed after installing package (downloaded at 1 task)
7. Show `dnsmasq` package information.
8. Check to what package `/etc/dnsmasq.conf` file belongs
9. Install `tmux` package
10. Update all packages
11. Upgrade `tmux` package
12. Find all packages related to `apache`
13. Check what packages includes `httpd.conf` file
14. Show all information and all related files to `logrotate` package
15. Verify `logrotate` package installation
16. Try to remove `logrotate` package
17. Rebuild RPM database

<br>

## ## Verify the integrity and availability of resources
1. Run a Memory test routine on a node.
2. Run `fsck` on a filesystem
3. Schedule a filesystem check at reboot
4. Verify the integrity of rpm database

<br>

## ## Verify the integrity and availability of key processes
1. Display running processes in a forest output
2. Display running processes in a forest output grep for cron process and exclude grep from to results
3. Examinate `htop`

<br>

## ## Change kernel runtime parameters, persistent and non-persistent
1. View all current kernel parameters
2. Make a non-persistent change and disable auto close for cdrom (with `sysctl` and writing to file)
3. View the `dev.cdrom.autoclose` parameter value
4. View what is the current value for `net.ipv4.icmp_echo_ignore_all`
5. Make a non-persistent change and enable ICMP echo ignoring
6. Make a persistent change and enable `net.ipv4.ip_forward`
7. Make a persistent change and disable `net.ipv6.conf.all.accept_ra`
8. Save the persistent changes, reboot the system and check if they're the same.

<br>

## ## Use scripting to automate system maintenance tasks
1. Create a simple `bash` script that will output current directory, and the user who is executing it.
2. Create a simple `bash` script that will read two values from command line and will compare them.
3. Create a simple `bash` script that will read a path from command line and will check if the directory exists.
4. Create a simple `bash` script that will print numbers from 0 to 9
5. Create a simple `bash` script that will read `url` variable from a file and will curl all the websites that are located in that file
6. Create a simple `bash` script that will count up to 5 using `until`
7. Create a simple `bash` script that will increase file size until 1KB.

<br>

## ## Manage the startup process and services (In Services Configuration)
1. Check the status of `dnsmasq` service.
2. Stop `dnsmasq` service and check the status
3. Start `dnsmasq` service again and check the status.
4. Try to restart `dnsmasq` service.
5. Install `ntp` service and enable it.
6. Disable `dnsmasq` service.
7. Check if ntp service is enabled
8. List all systemd unit objects available

<br>

## ## List and identify SELinux/AppArmor file and process contexts
1. Show all available `selinux` contexts
2. List files and show their context
3. List all processes and show their context
4. Check what is the current context of `selinux`
5. Change the default context to something else

<br>

## ## Manage Software
1. Search for a `apache` package
2. Show information about `dnsmasq` package
3. Install `tmux` package
4. Remove `tmux` package
5. Check which repo/package contains `/etc/dnsmasq.conf` file
6. Run autoremove
7. Download `tmux` RPM package via yum

<br>

## ## Identify the component of a Linux distribution that a file belongs to
1. Search a for a package that contains `httpd.conf` file
2. Show all libraries used for `/sbin/ip` command
3. Show all libraries used for `vi`
4. Rebuid library cache.
