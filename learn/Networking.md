# Chapter 4. Networking

## ## Configure networking and hostname resolution statically or dynamically
**Interface configuration**
  - Interface configuration files can be found in the `/etc/sysconfig/network-scripts` directory
  - Directory should contain one file per interface
  - Interface configuration file example:
```
BOOTPROTO=none
DEVICE=ens5
DHCPV6C=yes
HWADDR=06:a1:35:77:b4:c1
IPV6INIT=yes
ONBOOT=yes
STARTMODE=auto
TYPE=Ethernet
IPADDR=10.10.10.10
PREFIX=24
GATEWAY=10.10.10.1
DNS1=8.8.8.8
DNS2=8.8.4.4
```

  - `nmtui` - Network Manager Terminal User Interface. Interactive shell to configure network interfaces.
    - May be uninstalled by default, so need to run `yum install -y networkmanager-tui`

**Hostname setup**
  - `$ hostname` will show the current hostname of the server.
  - `$ hostnamectl set-hostname new-hostname` - will set hostname to `new-hostname`
  - Hostname can also be changed in `/etc/hostname` file.
  - **NOTE: Reboot is required to see the new hostname**

**Hostname resolution statically or dynamically**

**Static**
  - Static DNS entries can be added in `/etc/hosts` file
  - Example entry in the file:
    - `8.8.8.8 google-dns` - this will pin `google-dns` hostname/domain/host to resolve `8.8.8.8` ip address
    - `192.168.1.10 server.local` - this will pin `server.local` to `192.168.1.10` ip address.

**Dynamic**
  - `/etc/resolv.conf`
    - Nameservers has to be listed here for dynamic name resolution.
    - Example entry in the file:
      - `nameserver 1.1.1.1`
      - `nameserver 1.0.0.1`

**Useful commands**:
  - `$ ip addr show` - show interfaces, their configuration
  - `$ ip link set down eth0 && ip link set up eth0` - Will flap `eth0` port.

<br>

## ## Configure network services to start automatically at boot
`/etc/services` - Shows active network applications and ports
  - `systemctl enable NetworkManager.service`
  - `systemctl status NetworkManager.service`
  - `systemctl enable network`
  - `systemctl status network`

Besides that network services are started the same way as any other service.

<br>

## ## Implement packet filtering
**View current configuration**
  - `$ iptables -L`
  - `$ iptables -nvL`
  - `$ iptables -t table -nvL`

**Basic syntax of iptables command**
  - `iptables {-A|I} chain [-i/o interface][-s/d ipaddres] [-p tcp|upd|icmp [--dport|--sport nn…]] -j [LOG|ACCEPT|DROP|REJECTED]`
    - `{-A|I}`
      - `-A`: append the rule (it will be the last rule)
      - `-I`: insert the rule (it will be the first rule)
    - `[-i/o interface]`
      - `-i eth0`: the packet is received (input) via eth0 interface
    - `[-s/d ipaddres]`
      - `-s Source address`: ipaddres can be an address or a subnet
      - `-d Destination address`: ipaddres can be an address or a subnet
    - `[-p tcp|upd|icmp [--dport|--sport nn…]]`
      - `-p protocol`
    - `--dport`: Destination port
    - `--sport`: Source port
    - `-j [LOG|ACCEPT|DROP|REJECTED]`
      - `ACCEPT`: accept packet
      - `DROP`: silently rejected
      - `REJECTED`: reject the packet with an ICMP error packet
      - `LOG`: log packet. Evaluation of rules isn't blocked.

**Example to DROP incoming ICMP packets**
  - `$ iptables -A INPUT -p icmp -i ens5 -j DROP`
    - `-A`: Append to selected chain
    - `-p`: Protocol of the packet to check
    - `-i`: Name of interface to monitor
    - `-j`: Target of the rule (what to do if a match occurs)

**Example to ACCEPT all incoming traffic to loopback**
  - `iptables -A INPUT -i lo -j ACCEPT`

**Example to DROP all outgoing traffic to port 22**
  - `iptables -A OUTPUT -o eth1 -p tcp --dport 22 -j DROP`

**Example to Allow outgoing traffic if state is `ESTABLISHED` or `RELATED`**
  - `iptables -A OUTPUT -m state --state ESBLISTAHED,RELATED -j ACCEPT`

<br>

## ## Start, stop, and check the status of network services
Start, stop, check status can be done with `systemctl` command. Like any other service.

`netstat`- shows various network services, helps to identify what is running, what ports are in use, if connections are open and etc.

`netstat` examples:
  - `netstat -a`: lists all open sockets
  - `netstat -u`: list  udp (`-t` (tcp), `-x` (unix))
  - `netstat -at`: return all sockets for tcp
  - `netstat -lt`: return all tcp sockets that we're listening on
  - `netstat -au`: return all sockets for udp
  - `netstat -lu`: return all udp sockets that we're listening on
  - `netstat -t`: return all established tcp sockets
  - `netstat -u`: return all established udp sockets
  - `netstat -apt`: return all tcp sockets, includes pid/program name
  - `netstat -lpt`: return all tcp sockets that are in listen state, includes pid/program name
  - `netstat -su`: return statistics for udp packets
  - `netstat -st`: return statistics for tcp packets
  - `netstat -r`: return all routing information
  - `netstat -i `: return all interfaces information in packets
  - `netstat -ci`: live packet information about interfaces

**NOTE: `netstat` may not be installed by default, so need to run `yum install -y net-tools`

<br>

## ## Statically route IP traffic
**View routes**
  - `route -n`
    - Older command that may not be installed on newer distributions
  - `ip route show`
    - Part of the IP tools suite.

**Add static route**
  - `ip route add IP/MASK via GATEWAY dev INTERFACE`
    - `ip route add 1.1.1.1/32 via 192.168.0.100 dev eth1`
    - Will route through 192.168.0.100 when destination is 1.1.1.1
  - To create a persistent route, create a `route-ifname` file for the interface.
    - `vi /etc/sysconfig/network-scripts/route-eth1`
    - Add line `1.1.1.1/32 via 192.168.0.100 dev eth1`
    - Reload the file with `service network restart`

**Delete static route**
  - `ip route delete 1.1.1.1/32 via 192.168.0.100 dev eth1`

**Show packet path**
  - `traceroute 1.1.1.1`

**Add static IP address**
  - `ip address add 192.168.0.100/32 dev eth1`

<br>

## ## Synchronize time using other network peers
Many Linux services and applications depends on accurate system time across multiple nodes. With the move to `systemd` the venerable `ntp` command has been replaced by the `timedatectl` command.

**To view the time configuration**
  - `$ timedatectl`

**To enable or disable time sync**
  - `$ timedatectl set-ntp on/off`

**View and update time zone information**
  - `$ timedatectl list-timezones`
  - `$ timedatectl set-timezone TIMEZONE`

**Set time manually**
  - `$ timedatectl set-ntp false`
  - `$ timedatectl set-time 17:00`

`chronyd` is a default mechanism to synchronize time in CentOS
  - `/etc/chrony.conf` - configuration file
    - `server` parameters are servers that are used as source of synchronization
    - `chronyc source` contact server and show them status
    - `chronyc tracking` show current status of the system clock

