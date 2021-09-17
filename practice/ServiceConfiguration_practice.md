# Chapter 5. Service Configuration

## ## Configure a caching DNS server
1. Install BIND dns server
2. Configure DNS server
    1. Add lfcs-test.com zone
    2. Add reverse zone for it.
    3. Cache should be enabled
    4. Should listen on default port
3. Verify if DNS server is working

<br>

## ## Configure a caching DNS server
1. Create and configure lfcs-test.com zone
2. Create and configure reserver zone for it.
3. Check if configuration syntax is ok.
4. Verify if resolution works

<br>

## ## Configure email aliases
1. Create alias for `root` to `tomvil`
2. Create alias for `root` to `student` and `teacher`
3. Send and email to root
4. Check if root received email
5. Check if email was sent to aliases.

<br>

## ## Configure SSH servers and clients
1. Start and enable ssh server
2. Allow to login with root
3. Disable login with password (allow only with ssh keys)
4. Generate your ssh key
5. Upload it to remove server
6. Test if you can login without password

<br>

## ## Restrict access to the HTTP proxy server
1. Install Squid
2. Make ACL with all internal subnets
3. Make ACL with safe ports (80 and 443)
4. Allow traffic only to `safe ports` acl
5. Allow traffic from internal subnets acl
6. Deny all other traffic
7. Set coredump logs directory to `/var/log/squid`
8. Enable proxy and test if rules works.

<br>

## ## Configure an IMAP and IMAPS service
1. Install dovecot
2. Configure it to work.
3. Test it

<br>

## ## Query and modify the behavior of system services at various operating modes
1. Display all the service unit files for random service
2. Display all dependencies for random service
3. Create a drop-in for random service, that will restart always if stopped/crashed or etc.
4. View running systemd unit file of a random service
5. List all systemd units of type service
6. List all systemd units of type service and state inactive
7. Set Memory limit to 200M for a random service

<br>

## ## Configure an HTTP server
1. Install httpd server
2. Create new website
3. Test if it works

<br>

## ## Configure HTTP server log files
1. Create a new logformat to log hostname, date, request type, and browser used
2. Save logs with new logformat created to `/var/log/website.log`

<br>

## ## Configure a database server
1. Install MariaDB
2. Run init setup with all recommended settings
3. Log in to interactive shell
4. Create database `testing`
5. Create table `coding` in `testing` database
6. Add user `tomvil` that is allowed to login only from localhost
7. Add user `adminas` that is allowed to login from everywhere
8. Grant all permissions on testing database for both users
9. Login with new user and create table `permissions` and check if it works.

<br>

## ## Restrict access to a web page
1. Configure that only 192.168.100.100 can access `/var/www/html/test` website.
2. Configure the same just using `.htaccess` file.

<br>

## ## Manage and configure containers
1. Install docker
2. Start new container with image busybox
3. Start new webserver (httpd) on docker in the background. PF 8080 -> 80. Copy index.html from local to container.
4. Check all containers running
5. Check only active containers running
6. Check docker image list
7. Exeute a command on container
8. Login in to container
9. Stop container
10. Remove container
11. Remove image
12. Show container logs

<br>

## ## Manage and configure Virtual Machines
1. Install all needed packages to run qemu
2. Create new storage pool
3. Create virtual disk
4. Create virtual machine named test, with 1 vcpu, memory 2G, boot with centos7.iso image and add 5G of disk size. Console should be enabled
5. List all running virtual machines
6. Edit test machine
7. Enable autostart for test machine
8. Disable autostart for test machine
9. Stop test machine
10. Start test machine
11. Login via console to test machine.
12. Delete test machine
13. Add another machine with different parameters, name it as you want
14. Show all the information about that virtual machine
15. Show vcpu count of that virtual machine
16. Increase vcpu to 4
17. Increase memory to 2G
