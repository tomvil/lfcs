# Chapter 4. Networking

## ## Configure networking and hostname resolution statically or dynamically
1. Add a new interface (if using vm)
2. Configure that interface with settings below (while editing file):
    1. Static configuration
    2. IP = 192.168.128.100/24
    3. GATEWAY = 192.168.128.254
    4. DNSes = 1.1.1.1,8.8.8.8
    5. Check if everything works and reset it.
3. Repeat the same configuration while using `nmtui`
4. Change your systems hostname to `lfcs2020.local`
5. Add static dns binding. `lfcs111.local` should be resolved to `192.168.128.200`.
6. Add `8.8.8.8 and 1.1.1.1` as global DNSes.
7. List all interfaces and their statuses
8. Flap freshly added interface

<br>

## ## Configure network services to start automatically at boot
1. Install any network related service
2. Start and enable it
3. Investigate `/etc/services` file.

<br>

## ## Implement packet filtering
1. Make sure `iptables` is the only firewall running
2. Add rule to drop incoming ICMP packets
3. Add rule to accept all incoming traffic to loopback interface
4. Add rule to allow all outgoing traffic is state is ESTABLISHED or RELATED.
5. Add rule to drop 80/443 incoming connections
6. Add rule to drop outgoing traffic to port 179
7. Add rule to forward port 2020 to 192.168.128.100:80
8. Add rule to to SRC NAT (192.168.128.100 to 88.77.66.55)

<br>

## ## Start, stop, and check the status of network services
1. List all open sockets
2. List only udp open sockets
3. List only tcp open sockets
4. List only unix open sockets
5. List all sockets for tcp
6. Return all UDP sockets, which server is listening on
7. Return all tcp sockets and include pid/program name
8. Return all tcp sockets that are in listen state, include pid/program name.
9. Return statistics for udp packets
10. Return statistics for tcp packets
11. Return all routing information
12. Return all interfaces information (in packets)
13. Live packet information about any system network interface.

<br>

## ## Statically route IP traffic
1. Add non-persistent static route to 8.8.8.8 via 192.168.128.3
2. Delete that non-persistent static route
3. Add persistent static route to 1.1.1.1 via 192.168.128.203
4. Delete that persistent route.
5. Check what are the path to 1.1.1.1 destination.

<br>

## ## Synchronize time using other network peers
1. Show the current time configuration
2. Enable time synchronization
3. List all available timezones
4. Change timezone to GMT+2
5. Set time manually to 19:30
6. Check if clock is synchronized
7. Change NTP server in `chronyd`
