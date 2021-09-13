# Chapter 5. Service Configuration

## ## Configure a caching DNS server
DNS resolution is a key part of system communication and a caching DNS server can help improve performance.

One of Linux DNS servers is `BIND`
  - `$ yum install bind-utils` - will install BIND dns server
  - `/etc/bind/named.conf` - main configuration file
    - `/etc/bind/named.conf.options`
  - Configuration example:

```
options {
        listen-on port 53 { 127.0.0.1; 192.168.0.0/24; };
		...
        allow-query        { localhost; 192.168.0.0/24; };
        allow-query-cache  { localhost; 192.168.0.0/24; };
		...
        recursion yes;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
		...
};

zone "test.com." IN {
        type master;
        file "/var/named/test.com.zone";
};

zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "/var/named/rev.test.com.zone";
};
```
  - `listen-on port 53` tell server on which network interface and port to listen
  - `allow-query` defines the networks from which clients can send DNS requests
  - `allow-query-cache` defines the addresses/network from which clients are allowed to sent queries that can access the local cache
  - `forwarders` specifies upstream dns servers, they're used if name can't be resolved directly.
  - `zone` contains domain configuration. After `zone`, specify the name of the domain to administer.
    - `file` specifies which file stores zone data for the domain.
  - `zone "0.168.192.in-addr.arpa"` is the configuration for reserve zone or reverse lookup. A reverse zone allows DNS to convert from an address to a name.
  - `systemctl start named`

<br>

## ## Maintain a DNS zone
DNS is the contact list of the Internet. If you have a domain you might need to create the appropriate zone files for your DNS server.

In the last section we've created zone which points to `/var/named/test.com.zone`. Here are the contents of the file:
```
$TTL 3H
@       IN SOA  dns root.test.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN NS   dns
        IN MX   10 email

dns     IN A    192.168.0.29
email   IN A    192.168.0.29
web     IN A    192.168.0.29
www.web IN CNAME web
```

  - Line 2. This is where the SOA (start of authority) control record begins.
    - `@` means that zone will be extracted from the corresponding entry in `/etc/named.conf` (test.com in this example)
    - `dns` is the name of authoritative server for the zone
    - `root.test.com` an email address of the person in charge of this name server. For `root@test.com` the entry has to be `root.test.com.`
  - Line 8. The `IN NS` specifies  the name server responsible for this domain (authoritative server)
  - Line 9. The `MX` record specifies the mail server that accepts, processes and forwards emails for this domain.
  - Last Lines. These are the actual address records where one or more IP addresses are assigned to hostnames.
    - CNAMES maps a name on another name`

Another file was mentioned earlier `/var/named/rev.test.com.zone` contents:
```
$TTL 3H
@       IN SOA  dns.test.com. root.test.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN NS   dns.test.com.

29      IN PTR  dns.test.com.
```

  - Line 2. The configuration file should activate reverse lookup for the network `192.168.1.0`. Given that the zone is called `1.168.192.in-addr.arpa`, should not be added to the hostnames. Therefore, all hostnames are entered in their complete form with their domain and with `.` at the end. The remaining entroes correspond to those described for the `test.com.` zone.
  - Line 8. This line specifies the name server responsible for this zone. This time, the name is entered in its complete form with the domain and a `.` at the end.
  - Line 10. This is the pointer record hinting at the IP addresses on the respective hosts. Only the last part of the IP address is entered at the beginning of the line, without the `.` at the end.

**NOTE: More examples of configuration can be found at /usr/share/doc/bind-{{version}}/sample**

**To test if BIND configuration syntax is ok**
  - `named-checkconf`

**To test if name resolution is working correctly**
  - `host dns localhost` (dns - domain, localhost - dns server)
  - `host 192.168.0.100 localhost` for reverse dns resolution

<br>

## ## Configure email aliases
**Alias types**
  - Internal Alias - Single Account
    - `source-account: destination-account`
    - `root: tomvil` creates alias `root => tomvil`
  - Internal Alias - List of accounts
    - `source-account: destination-accounts`
    - `root: tomvil, viltom` create alias `root => tomvil, viltom`
  - External Alias
    - `source-account: example@domain.com`

**To create alias**
  - `/etc/aliases`
    - Add one of the aliases mentioned before
  - run `$ newalises` to apply changes

**To send an email and test if alias works**
  - `yum install -y mailx`
  - `echo "Test Message" | mail -s "Subject" root`
  - Switch to root and execute `mailx` to view mail messages.

<br>

## ## Configure SSH servers and clients
Secure Shell is the standard remote access protocol used by Linux system administrators for connecting to servers anywhere in the world.

**SSH Configuration Files**
  - System-wide server configuration
    - `/etc/ssh/sshd_config`
    - Common configuration options to do:
      - `PermitRootLogin no` Disable root login with ssh client
      - `PasswordAuthentication no` Disable login with password (can log in only with ssh key)
  - System-wide client configuration
    - `/etc/ssh/ssh_config`
    - Common configuration options to do:
      - `ForwardX11 yesz allows use of X11 server with ssh.
  - User specific configuration
    - `~/.ssh/config`

**Server Management**
  - `systemctl status sshd` to check ssh server status
  - `systemctl stop sshd` to stop ssh server
  - `systemctl start sshd` to start ssh server
  - `systemctl restart sshd` to restart ssh server
  - `systemctl enable sshd` to enable ssh server at boot
  - `systemctl disable sshd` to disable ssh server at boot

**SSH Key Generation and Sharing**
  - Use `ssh-keygen` command to create key pair
  - Keys can be found in the `~/.ssh/` directory.
    - `rsa_id` - the private key. Do not move or share.
    - `rsa_id.pub` - the public key. This one can be shared.
  - Use `ssh-copy-id` to copy your public key to another server
    - `$ ssh-copy-id remote_user@remote_server`
    - `$ ssh-copy-id root@192.168.0.100`

<br>

## ## Restrict access to the HTTP proxy server
**About Squid**
  - Squid is a common proxy server. A proxy server acts as a gateway or filter to control traffic and requests coming into your servers.
  - The configuratoin for Squid can be  found in the `/etc/squid/squid.conf` file.
    - **NOTE: The default configuration file is enormous but is fully documented**

**Squif configuration**
  - `acl`: access control list
  - `http_access`: grant or deny access based ACLs
  - `http_port`: port that squid listens to
  - `core_dump`: directory where core dump files are stored
  - `refresh_pattern`: values used to determine if a cached objects is stale and should be refreshed.

**Configuration examples**
  - Define acl name and subnets/addresses/ports and etc. in it
    - `acl localnet src 10.0.0.0/8`
      - Creates acl named `localnet` add source range `10.0.0.0/8`
    - `acl localnet src 172.16.0.0/12`
    - `acl localnet src 192.168.0.0/16`
    - `acl target src 10.10.10.10`
    - `acl SSL_port 443`
    - `acl Safe_ports port 80`
    - `acl Safe_ports port 443`
  - Deny requests based on ACL's
    - `http_access deny !Safe_ports`
      - Will deny all that is not defined in `safe_ports` acl
    - `http_access allow localnet !target`
      - Will allow all traffic from localnet except the `target` acl
    - `http_access deny all`
      - Will deny all traffic.
  - Define coredump_dir
    - `coredump_dir /var/log/squid`
      - Core logs will be saved in `/var/log/squid` directory
  - Configure refresh pattern
    - refresh_pattern ^ftp:           1440    20%     10080
    - refresh_pattern ^gopher:        1440    0%      1440
    - refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
    - refresh_pattern .               0       20%     4320

**To use http proxy**
  - `http_proxy` environment variable must be configured in order to use a proxy server
    - `export http_proxy=http://127.0.0.1:3128` uses a local proxy server that is listening on 3128 port
    - `export http_proxy=http://username:password@127.0.0.1:3128` If authentication is required.

<br>

## ## Configure an IMAP and IMAPS service
**Mail protocols**
  - IMAP
    - Internet Message Access Protocol
    - When reading a message cia IMAP the message are not automatically downloaded
    - Messages are not downloaded until they are clicked on
    - All messages remain on the server
  - POP3
    - Post office protocol
    - POP contacts the mail provider and downloads all new messages first, then makes them available to read
    - Once POP downloads a message, it is deleted from the server and is only available on the local device.

**Dovecot configuration**
  - `yum install -y dovecot` - install dovecot first.
  - `/etc/dovecot/dovecot.conf` - primary configuration file.
    - `protocols = imap pop3` enable imap and pop3 protocol
  - `/etc/dovecot/conf.d/` - Contains dovecot configuration files
    - `10-mail.conf` - Check `mail_location` and `mail_privileged_group`
    - `10-ssl.conf` - SSL configuration file
    - `20-imap.conf/20-pop3.conf` - Protocol configuration files
  - `/etc/dovecot/private` - contains the SSL keys for the environment.

**NOTE: When installed on CentOS certificates are generated in `/etc/pki/dovecot/private`. It should be listed automatically in `10-ssl.conf` file. If it's not you can generate them via `mkcert.sh`**
  - `/usr/share/doc/dovecot/2.2.36/mkcert.sh`
  - `/usr/libexec/dovecot/mkcert.sh`

<br>

## ## Query and modify the behavior of system services at various operating modes
**sysmtectl command options**
  - `systemctl cat service.name`
    - Displays all the service unit files
  - `systemctl list-dependencies service.name`
    - Displays everything required to start the service
  - `systemctl edit service.name`
    - Creates a service override file in the `/etc/systemd/system` directory (aka drop-in)
  - `systemctl edit --full service.name`
    - Edits the running systemd unit file
  - `systemctl list-units`
    - When combined with options can display service details in different formats
    - `systemctl list-units --type=service` - will list systemctl units of type service
    - `systemctl list-units --type=service --state=inactive --all` - will list all systemctl units of type service with state inactive.
  - `systemctl set-property service.name MemoryLimit=500M`
    - Will set MemoryLimit=500M for service.name at runtime.

<br>

## ## Configure an HTTP server
**Install apache (httpd) server**
  - `yum install -y httpd`
  - `systemctl start httpd`
  - `systemctl enable httpd`

**HTTPD Configuration**
  - `/var/www/html`
    - The default websites `home` directory
  - `/etc/httpd`
    - The httpd application directory. Contains all of the configuration directories and files.
  - `/etc/httpd/conf/`
    - Main configuration files are located here.
  - `/etc/httpd/conf.d/`
    - Virtuals host can be created inserting a `conf` file in this directory
    - File structure can be copied from `/usr/share/doc/httpd-version/httpd-vhosts.conf`


<br>

## ## Configure HTTP server log files
**Httpd log file details**
  - Log file formats are stored in the `httpd` configuration file.
  - The `error.log` format cannot be changed, only the `access.log` format can be changed.
  - Formatting values:
    - `%h` = Hostname (ip address if name resolution is disabled)
    - `%u` = User (if site is authenticated)
    - `%t` = Date and time request was made
    - `%r` = The type of request
    - `%>s` = Status of the request (200, 404, etc).
    - `\"%{User-agent}i\"` = Browser used to make the request
  - Example:
    - `LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{UserAgent}i\" name_of_format`
    - `LogFormat "%{Referer}i -> %U" referer`

**Example**
```
ErrorLog /var/log/httpd/example.com_error_log
LogFormat %s %v combined
CustomLog /var/log/httpd/example.com_access_log combined
```
  - `ErrorLog` file where errors will be saved
  - `LogFormat` - custom log format named `comobined`
  - `CustomLog` where custom logs will be safed with `combined` logformat

Normally, logs are stored in `/var/log/httpd`

<br>

## ## Configure a database server
Most popular databases are `MySQL` or `MariaDB`

**Managing MariaDB:**
  - Best practices:
    - Always set a MariaDB root password
    - Disable anonymous accounts when possible
    - Disable MariaDB remote root login access when possible
  - MariaDB has a shell environment.
    - `mariadb -u username -p` will enter the shell environment.
    - Shell allows you to view and manage databases, run sql commands and perform administrative tasks.

**Installing MariaDB**
  - `yum install -y mariadb mariadb-server`

**Configuration init**
  - `mysql_secure_installation`
    - Will start interactive shell to set default configuration for database server.

**Shell command examples**
  - Create database
    - `create database dabase_name;`
    - `create database wordpress_app;`
  - Create user
    - `create user 'username'@'domain' identified by password;`
    - `create user 'tomas'@'localhost' identified by 123456;`
  - GRANT user privileges on database
    - `GRANT all on wordpress_app.* to 'tomas'@'localhost';`
    - `FLUSH PRIVILEGES;` - to update privileges.

<br>

## ## Restrict access to a web page
Access restrictions can be configured in websites `conf` file.

**Best practices**
  - Always make a backup copy of the configuration file before making any changes
  - Using a `apachectl checkconfig` command to validate configuration files

Example:
```
<Directory /var/www/html/test/>
  Order allow, deny
  Allow from 192.168.1.50
  Allow from 192.168.100.100
  Allow from 127
  Allow ::1
</Directory>
```
Will alow from mentioned ip addresses to mentioned directory, but will deny any other connections.

**How to handle it with .htaccess file**
  - Edit `/etc/httpd/conf/httpd.conf` file
    - `AllowOverride All` change to this, if it's not set already
  - In subdirectory of `/var/www` create .htaccess file.

Example:
```
Order Deny, Allow
Deny from 192.168.3.1
```
Will deny from 192.168.3.1 and allow everything else.


<br>

## ## Manage and configure containers
Containers enable a Linux system administrator to collect an application, dependencies and data into a single image that can be easily transferred and managed identically on different systems.

**Docker Basics**
  - `yum install -y docker` install docker
  - Docker commands require elevated privileges to run
  - Docker containers are build from images downloaded from the Docker Hub
  - Commands:
    - `docker ps` lists all active containers
    - `docker ps -a` list all containers (inactive as well)
    - `docker image list` lists all downloaded container images
    - `docker run` runs a command in a new container. Used to create and start a new container.
    - `docker start/stop` starts or stops container
    - `docker rm` removes container
    - `docker rmi` removes image
    - `docker logs` show container logs

**Example (run http server with port mapping)**
  - `docker run -dit --name test-web -p 80:80 -v /home/cloud_user/html:/usr/local/apache2/htdocs httpd`
    - `--name` container name
    - `-p 80:80` bind system 80 port to containers 80 port
    - `-v` copy `/home/cloud_user/html` contents to containers `/usr/local/apache2/htdocs` directory
    - `httpd` image name
    - `-dit`
      - `-d` detach mode (will be executed in background)
      - `-i` connects to standard input to container
      - `-t` get pseudo terminal

**Examples**
  - `docker run busybox ls`
    - will run busybox image and execute `ls` command

<br>

## ## Manage and configure Virtual Machines
**Install all tools needed to mange and configure virtual machines**
  - `yum install qemu-kvm qemu-img libvirt virt-install libvirt-client virt-manager bridge-utils`
  - `systemctl start libvirtd`

**Manage storage volume**
  - Storage Pool => container of storage volumes (directory, partitions)
  - Storage Volume => virtual disk
  - Create Storage Pool
    - `virsh pool-define-as spool dir - - - - "/media/vdisk/"`
    - `virsh pool-build`
    - `virsh pool-start`
    - `virsh pool-autostart`
    - In `/etc/libvirt/storage/*.xml` you can find information about storage pools.
  - Create Virtual Disk
    - `qemu-img create -f raw /media/vdisk/disk.img 1G`

**Manage Virtual Machines**
  - `virt-install --name=tiny --vcpus=1 --memory=1024 --cdrom=image.iso --disk size=5`
  - `virt-install --name=rthel7 --disk path=/mnt/vms/pool1/vol1.img,size=2 --vcpu=1 --ram=2014 --location=/root/rhel-server.iso --network bridge=virbr0 --graphics non --extra-args console=ttyS0`
    - This will p repare new virtual machine that is named `rhel7`. Will add 1 vcpu, 1G of RAM and a virtual disk of 2G size.
    - After creation, virtual machine will be booted for the first time and add a provided ISO image will be executed.
    - Virtual Machine is configured not to use graphical env and plus a configuration to allow a connection from the local machine is set.

**Virtual Machine Management**
  - `virsh list --all` will list all images (virtual machines)
  - `virsh edit VM_NAME` will edit machine configuration
  - `virsh start vm_name` will start machine
  - `virsh autostart vm_name` will enable machine to boot at startup.
  - `virsh autostart --disable vm_name` will disable machien to boot at startup.
  - `virsh stop vm_name` will stop machine
  - `virsh destroy vm_name` force shutdowny
  - `virsh undefine vm_name` delete virtual machine
  - `virsh console vm_name` will connect to machine via console. (ctrl+5 to exit)

**Edit Virtual Machine**
  - `virsh dominfo vm_name` shows virtual machine information
  - `virsh edit vm_name` edit configuration file of virtual machine
  - `virsh vcpucount vm_name` shows the number of virtual cpu.
  - `virsh setvcpus --count 2 vm_name` sets maximum number of virtual cpu
  - `virsh setmaxmem --size 2G vm_name` sets maximum amount of virtual machine memory
  - `virsh setmem --size 2G vm_name` sets amount of memory.
