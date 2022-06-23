---
description: '109.3: Basic network troubleshooting v2  Weight: 4'
---

# Chapter 15: Network Troubleshooting

<details>

<summary>Key terms</summary>

<mark style="color:red;">`ifdown`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> A script that calls the ip command to configure network interfaces. `ifdown <interface>` is used to bring down a network interface.

<mark style="color:red;">`ifup`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> A script that calls the ip command to configure network interfaces. `ifup <interface name>` is used to brin up a network interface.

<mark style="color:red;">`ip`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The ip command is replacing the `ifconfig` command that is becoming obsolete in some Linux distribution. Several important manners are its increased functionality and set of options, it can almost be one-stop-shop for configuration and control of a system's networking.

<mark style="color:red;">`netcat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The netcat is a utility that has many features for monitoring and debugging network connections.

<mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Used by the system administrator to monitor the traffic on the network and check connections that are not trustworthy.

<mark style="color:red;">`ping`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Packet Internet Groper (ping) command test basics connectivity betweens hosts by sending an ICMP echo request.

<mark style="color:red;">`ping6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The ping6 command is similar to the ping command, but it uses ICMPv6 ECHO\_REQUESTS to verify network connectivity

<mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The ss command is designed to show socket statistics and supports all the major packet and socket types.

<mark style="color:red;">`tracepath`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Used to trace the path to a network host, discovering MTU (Maximum Transmission Unit) along the path

<mark style="color:red;">`tracepath6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Used to determine what route communications travel between local and remote systems.

<mark style="color:red;">`traceroute`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Used to trace the route of packets to a specified host.

<mark style="color:red;">`traceroute6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Used for seeing how a transmission travels between local host machine to a remote system for IPv6 connections.

</details>

## Introduction

Once a network has been configured, the system administrator will inevitably encounter problems that will require troubleshooting. Root privileges are required in order to execute command options that update network settings.

Some commonly-observerd network issues are:

* Network interface card not detected by the system
* IP address not assigned correctly to the system
* Not being able to communicate with the router
* DNS service not reachable

## Display Network Status

Whne troubleshooting a network, it is useful to gather information about networking services such as open ports, interface statistics, routing tables, and network connections.

### Using `netstat`

The `netstat` command is used by the system administrator to monitor the traffic on the network, and check connections that are not trustworthy.

> It is a legacy command that is being phased out as new systems come online. The `ss` command should be used in most cases.

To **list all ports,** execute the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command as follows:

```bash
sysadmin@localhost:~$ netstat                                                   
Active Internet connections (w/o servers)                                       
Proto Recv-Q Send-Q Local Address           Foreign Address         State       
Active UNIX domain sockets (w/o servers)                                        
Proto RefCnt Flags       Type       State         I-Node   Path                 
unix  4      [ ]         DGRAM                    237543   /dev/log             
unix  2      [ ]         DGRAM                    240199                        
unix  2      [ ]         DGRAM                    236517
```

The output of the previous command list all ports, **including those that are not currently being used.** Those ports that are currently being used (active) are marked with the state `LISTEN` .

To **view only the listening port**, execute the <mark style="color:red;">`netstat -l`</mark> command.

```bash
sysadmin@localhost:~$ netstat -l                                                
Active Internet connections (only servers)                                      
Proto Recv-Q Send-Q Local Address           Foreign Address         State       
tcp        0      0 localhost:953           0.0.0.0:*               LISTEN      
tcp        0      0 localhost:domain        0.0.0.0:*               LISTEN      
tcp        0      0 localhost:domain        0.0.0.0:*               LISTEN      
tcp        0      0 127.0.0.11:35125        0.0.0.0:*               LISTEN      
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN      
tcp6       0      0 [::]:domain             [::]:*                  LISTEN      
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN      
udp        0      0 127.0.0.11:42894        0.0.0.0:*                           
udp        0      0 localhost:domain        0.0.0.0:*                           
udp        0      0 localhost:domain        0.0.0.0:*                           
udp6       0      0 [::]:domain             [::]:*                              
```

To **display a summary of details for each protocol**, execute the <mark style="color:red;">`netstat -s`</mark> command:

```bash
sysadmin@localhost:~$ netstat -s                                                
Ip:                                                                             
    1621 total packets received                                                 
    0 forwarded                                                                 
    0 incoming packets discarded                                                
    1621 incoming packets delivered                                             
    372 requests sent out                                                       
    4776 dropped because of missing route                                       
Icmp:                                                                           
    122 ICMP messages received                                                  
    80 input ICMP message failed.                                               
    ICMP input histogram:                                                       
        destination unreachable: 106                                            
        echo requests: 8                                                        
        echo replies: 8                                                         
    122 ICMP messages sent                                                      
    0 ICMP messages failed                                                      
    ICMP output histogram:                                                      
        destination unreachable: 106                                            
        echo request: 8                                                         
        echo replies: 8                  
```

To view the kernel's routing table, execute the `netstat -r` command:

```bash
sysadmin@localhost:~$ netstat -r                                                
Kernel IP routing table                                                         
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface  
192.168.1.0     *               255.255.255.192 U         0 0          0 eth0   
192.168.1.0     *               255.255.255.0   U         0 0          0 eth0
```

To **view the details of specific interfaces,** use the interface <mark style="color:red;">`-i`</mark> option when executing the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command:

```bash
sysadmin@localhost:~$ netstat -i                                                
Kernel Interface table                                                          
Iface   MTU Met   RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg  
eth0       1500 0      1369      0      2 0            57      0      0      0 BMRU                                                                             
eth0:0     1500 0       - no statistics available -                        BMRU 
lo         1500 0       268      0      0 0           268      0      0      0 LRU
```

> If you include the <mark style="color:red;">**`-c`**</mark>** option**, the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command will **display the interface information continuously** after an interval of 1 second.

The <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is also commonly **used to display open **_**ports**_. To see a list of all currently open ports, use the <mark style="color:red;">`netstat -tln`</mark> command:

```bash
root@localhost:~# netstat -tln                                                
Active Internet connections (only servers)                                    
Proto Recv-Q Send-Q Local Address           Foreign Address         State     
tcp        0      0 192.168.1.2:53          0.0.0.0:*               LISTEN    
tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN    
tcp        0      0 127.0.0.1:953           0.0.0.0:*               LISTEN    
tcp6       0      0 :::53                   :::*                    LISTEN    
tcp6       0      0 :::22                  :::*                    LISTEN   
tcp6       0      0 ::1:953                 :::*          
```

> <mark style="color:red;">`-t`</mark> stands for TCP, <mark style="color:red;">`-l`</mark> stands for listening (which ports are listening) and <mark style="color:red;">`-n`</mark> stands for show numbers, not names.

Sometimes showing the names can be more useful, this can be achieved by leaving out the `-n` option:

```bash
root@localhost:~# netstat -tl                                                   
Active Internet connections (only servers)                                      
Proto Recv-Q Send-Q Local Address           Foreign Address          State     
tcp        0      0 cserver.example.:domain *:*                     LISTEN    
tcp        0      0 localhost:domain        *:*                     LISTEN    
tcp        0      0 *:ssh                   *:*                     LISTEN    
tcp        0      0 localhost:953           *:*                     LISTEN    
tcp6       0      0 [::]:domain             [::]:*                  LISTEN    
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN    
tcp6       0      0 localhost:953           [::]:*       
```

{% hint style="info" %}
While no further development is being done on the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, it is still an excellent tool for displaying network information. The goal is to eventually replace the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command with commands such as the <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`ip`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> commands.
{% endhint %}

### Using `ss`

The <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is designed to **show socket statistics** and supports all the major packet and socket types.

A _network socket_ is a communication endpoint between nodes (devices) on a network. **Sockets use a socket address to receive incoming network traffic and forward it to a process on a machine or service.**

{% hint style="success" %}
The socket address commonly **consists of the IP address of the node** that it is "attached" to **and a port number.**
{% endhint %}

The main reason a user would use the <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is to **view what connections are currently established between their local machines and remote machines**, statistics about those connections, etc. To use the <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, follow the syntax below:

```bash
ss [options] [FILTER]
```

Similar to the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, you can get a great deal of useful information from the <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command just by itself, as shown in the example below:

```bash
root@localhost:~# ss
Netid  State      Recv-Q Send-Q       Local Address:Port               Peer Address:Port   
u_str  ESTAB      0      0                    * 104741                    * 104740 
u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 14623      * 14606  
u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 13582      * 13581  
u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 16243      * 16242  
u_str  ESTAB      0      0                    * 16009                     * 16010  
u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 10910      * 10909  
u_str  ESTAB      0      0      @/tmp/dbus-LoJW0hGFkV 15706                * 15705  
u_str  ESTAB      0      0                    * 24997                     * 24998  
u_str  ESTAB      0      0                    * 16242                     * 16243  
u_str  ESTAB      0      0          @/tmp/dbus-opsTQoGE 15471  
```

The output is very similar to the output of the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command with no options. The columns above are:

* `Netid` - the socket type and transport protocol
* `State` - Connected or Unconnected, depending on the protocol
* `Recv-Q` - Amount of data queued up for being processed having been received
* `Send-Q` - Amount of data queued up for being sent to another host.
* `Local Address` - The address and port of the local host's portion of the connection
* `Peer Address` - The address and port of the remote host's portion of the connection.

The <mark style="color:red;">`-s`</mark> option to the <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command displays mostly the **types of sockets**, **statistics about their existence** and numbers of **actual packets sent and received** via each socket type, as shown below:

```bash
root@localhost:~# ss -s
Total: 1000 (kernel 0)
TCP:   7 (estab 0, closed 0, orphaned 0, synrecv 0, timewait 0/0), ports 0
 
Transport Total     IP        IPv6
*          0         -         -        
RAW          0         0         0        
UDP          9         6         3        
TCP          7         3         4        
INET         16        9         7        
FRAG         0         0         0
```

One common use for the <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is determining which ports an interface is _listening_ on. By using the <mark style="color:red;">`-l`</mark> option to **list only those ports which are listening**, and the <mark style="color:red;">`-t`</mark> option to **show only TCP ports**,  you can determine which ports are **available for TCP connection:**

```bash
sysadmin@localhost:~$ ss -lt                                                    
State    Recv-Q    Send-Q        Local Address:Port         Peer Address:Port   
LISTEN   0         128              127.0.0.11:33906             0.0.0.0:*      
LISTEN   0         10              192.168.1.2:domain            0.0.0.0:*      
LISTEN   0         10                127.0.0.1:domain            0.0.0.0:*      
LISTEN   0         128                 0.0.0.0:ssh               0.0.0.0:*      
LISTEN   0         128               127.0.0.1:953               0.0.0.0:*      
LISTEN   0         10                     [::]:domain               [::]:*      
LISTEN   0         128                    [::]:ssh                  [::]:*      
LISTEN   0         128                   [::1]:953   
```

When troubleshooting UDP dependent service like DNS name resolution or some video streaming applications, you can use the <mark style="color:red;">`-u`</mark> option to **display just the UDP sockets available**.

```bash
sysadmin@localhost:~$ ss -ul                                                    
State    Recv-Q    Send-Q        Local Address:Port         Peer Address:Port   
UNCONN   0         0               192.168.1.2:domain            0.0.0.0:*      
UNCONN   0         0                 127.0.0.1:domain            0.0.0.0:*      
UNCONN   0         0                127.0.0.11:47117             0.0.0.0:*      
UNCONN   0         0                      [::]:domain      
```

### Using `ip`&#x20;

The <mark style="color:red;">`ifconfig`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is becoming obsolete in some Linux distributions and is being replacedd with a form of the <mark style="color:red;">`ip`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, specially <mark style="color:red;">`ip address`</mark> .

The format for the <mark style="color:red;">`ip`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is as follows:

```
ip [OPTIONS] OBJECT COMMAND
```

While the <mark style="color:red;">`ifconfig`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> is limited primarly to the modification of networking parameters, and displaying the configuration details of networking components, **the **<mark style="color:red;">**`ip`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command branches out to do some of the work of several other legacy command such as **<mark style="color:red;">**`route`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** and **<mark style="color:red;">**`arp`**</mark>.

```bash
sysadmin@localhost:~$ ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0c:29:71:f0:bb  
          inet addr:172.16.241.140  Bcast:172.16.241.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe71:f0bb/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:8506 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1201 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:8933700 (8.9 MB)  TX bytes:117237 (117.2 KB)
 
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:285 errors:0 dropped:0 overruns:0 frame:0
          TX packets:285 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:21413 (21.4 KB)  TX bytes:21413 (21.4 KB)


sysadmin@localhost:~$ ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever 
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:71:f0:bb brd ff:ff:ff:ff:ff:ff
    inet 172.16.241.140/24 brd 172.16.241.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe71:f0bb/64 scope link 
       valid_lft forever preferred_lft forever
```

Both command show the type of interface, protocols, hardware and IP addresses, network masks, and various other pieces of information about each of the active interfaces on the system.

Another useful option with the <mark style="color:red;">`ip`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is the <mark style="color:red;">`-statistics`</mark> or <mark style="color:red;">`-s`</mark> option, which shows statistics for the object referenced in the command. For example, to **show statistics for active IP addresses**, you can type:

```bash
root@localhost:~# ip -s address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
   link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
   inet 127.0.0.⅛ scope host lo
      valid_lft forever preferred_lft forever
   inet6 ::1/128 scope host
      valid_lft forever preferred_lft forever
   RX: bytes  packets  errors  dropped  overrun  mcast
   6680       88       0       0        0        0
   TX: bytes  packets  errors  dropped  carrier  collsns
  6680      88        0       0         0        0
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_code1 state UP group default qlen 1000
   link/ether 52:54:00:12:34:56 brd ff:ff:ff:ff:ff:ff
   inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic ens3
     valid_lft 85864sec preferred_lft 85864sec
   inet6 fe80::5054:ff:fe12:3456/64 scope link
     valid_lft forever preferred_lft forever
   RX: bytes  packets  errors  dropped  overrun  mcast
   11020      29       0       0        0        0
   TX: bytes  packets  errors  dropped  carrier  collsns
   4278       39       0       0        0        0
```

{% hint style="info" %}
**Multiple dropped packets could indicate a Layer 1 issue** where network cabling could be the problem.
{% endhint %}

### Using `ping`

The _**packet internet groper**_ <mark style="color:red;">`ping`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to check the connectivity to a host**. It is a simple test that can be performed from the command prompt when a particular network service is not available.

This utility sends the ICMP protocol's **`ECHO_REQUEST` (ping)** **datagram** to a host, and the host sends an **`ECHO_RESPONSE` (pong) datagram** in response. Each datagram will have an IP header, an ICMP header, a timeval structure, and some additional bytes used for padding.

The time to live (**TTL**) value is the **maximum number of IP routers that may attempt to route a packet.** Every time a router attempts to route the packets, its TTL count is decremented by 1. If a router receives a packet and the TTL of a packet is zero, then the packet is discarded.

{% hint style="info" %}
Typically, a packet will have a maximum TTL of 30 hops by default.
{% endhint %}

The Linux <mark style="color:red;">`ping`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command continuously displays the TTL value of each packet it receives until **Ctrl-C** is pressed to cancel. The **count **<mark style="color:red;">**`-c`**</mark>** option stops after sending **_**n**_** packets** as seen in the example below, which sends 5 packets (<mark style="color:red;">`-c 5`</mark>) and then stops:

```bash
sysadmin@localhost:~$ ping -c 5 192.168.1.2                                     
PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.                            
64 bytes from 192.168.1.2: icmp_seq=1 ttl=64 time=0.065 ms                      
64 bytes from 192.168.1.2: icmp_seq=2 ttl=64 time=0.059 ms                      
64 bytes from 192.168.1.2: icmp_seq=3 ttl=64 time=0.056 ms                      
64 bytes from 192.168.1.2: icmp_seq=4 ttl=64 time=0.058 ms                      
64 bytes from 192.168.1.2: icmp_seq=5 ttl=64 time=0.068 ms 
 
--- 192.168.1.2 ping statistics ---                                             
5 packets transmitted, 5 received, 0% packet loss, time 4001ms                  
rtt min/avg/max/mdev = 0.056/0.061/0.068/0.006 ms
```

> The hostname can also be specified instead of the IP address as follows.

Some of the key options of the <mark style="color:red;">`ping`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command are:

| Option                                          | Meaning                                         |
| ----------------------------------------------- | ----------------------------------------------- |
| <mark style="color:red;">`-c count`</mark>      | Stop after sending count `ECHO_REQUEST` packets |
| <mark style="color:red;">`-s packetsize`</mark> | Specifies the number of data bytes to be sent   |
| <mark style="color:red;">`-t ttl`</mark>        | Sets the IP Time to Live                        |
| <mark style="color:red;">`-w timeout`</mark>    | Sets the timeout in seconds for ping to exit    |

The <mark style="color:red;">`ping6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is similar to the <mark style="color:red;">`ping`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, but it uses `ICMPv6 ECHO_REQUEST` to verify network connectivity. The <mark style="color:red;">`ping6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can use either a hostname or an IPv6 address to request a response from remote systems.

```bash
sysadmin@localhost:~$ ping6 ipv6.google.com
PING ipv6.google.com(dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e)) 56 data bytes
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=1 ttl=55 time=16.10 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=2 ttl=55 time=18.5 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=3 ttl=55 time=18.7 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=4 ttl=55 time=15.2 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=5 ttl=55 time=16.2 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=6 ttl=55 time=15.10 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=7 ttl=55 time=14.8 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=8 ttl=55 time=16.4 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=9 ttl=55 time=14.6 ms
64 bytes from dfw25s34-in-x0e.1e100.net (2607:f8b0:4000:808::200e): icmp_seq=10 ttl=55 time=14.10 ms
^C
--- ipv6.google.com ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 20ms
rtt min/avg/max/mdev = 14.563/16.227/18.664/1.388 ms
```

### Using `traceroute`

The <mark style="color:red;">`traceroute`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to trace the route of packets to a specified host**. This utility uses the IP header's TTL field and tries to fetch an ICMP `TIME_EXCEEDED` response from each router on the path to the host. The probing is done by sending ICMP ping packets with a small TTL value and then checking the ICMP `TIME_EXCEEDED` response.

Networks administrators use this command to test and isolate network problems. This command can be run by roout users only.

To trace the route to a particular host, execute the following command:

```bash
sysadmin@localhost:~$ traceroute example.com                                    
traceroute to example.com (192.168.1.2), 30 hops max, 60 byte packets           
 1  example.com (192.168.1.2)  0.026 ms  0.015 ms  0.013 ms
```

Some of the key options of the <mark style="color:red;">`traceroute`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command are:

| Option                                         | Meaning                                                                     |
| ---------------------------------------------- | --------------------------------------------------------------------------- |
| <mark style="color:red;">`-T`</mark>           | Probe using TCP SYN                                                         |
| <mark style="color:red;">`-f first_ttl`</mark> | Specifies the initial TTL value                                             |
| <mark style="color:red;">`-m max_ttl`</mark>   | Specifies the maximum number of hops to be probed                           |
| <mark style="color:red;">`-w timeout`</mark>   | Sets the timeout in seconds to exit after waiting for a response to a probe |

The <mark style="color:red;">`traceroute`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, commonly **used for seeing how a transmission travels between a local host machine to a remote system** can also be used for IPv6 connections. To use the <mark style="color:red;">`traceroute6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, which is the same as <mark style="color:red;">`traceroute -6`</mark> to view the IPv6 path to `ipv6.google.com` execute the following command:

```bash
sysadmin@localhost:~$ traceroute6 ipv6.google.com
```

```bash
sysadmin@localhost:~$ traceroute6 ipv6.google.com
traceroute to ipv6.l.google.com (2607:f8b0:4009:811::200e) from 2600:380:5c6b:897e:443b:aa22:6729:5980, 30 hops max, 24 byte packets
1 2600:380:5c6b:897e:3504:968f:d7a4:fbcc (2600:380:5c6b:897e:3504:968f:d7a4:fbcc)   2.219 ms 1.481 ms 1.725 ms
2 * * *
3 * * *
4 2600:300:2000:2604::1 (2600:300:2000:2604::1) 72.578 ms 52.919 ms 34.129 ms
5 2600:300:2000:2622::1 (2600:300:2000:2622::1) 40.852 ms 38 ms 40.568 ms
6 * * *
7 2001:1890:ff:ffff:12:83:188:242 (2001:1890:ff:ffff:12:83:188:242) 69.811 ms 67.35 ms 87.242 ms
8 cgcil21crs.ipv6.att.net (2001:1890:ff:ffff:12:122:2:225) 61.877 ms 96.87 ms 77.492 ms 
9 2001:1890:ff:ffff:12:122:22:52 (2001:1890:ff:ffff:12:122:22:78.746 ms 82.986 ms 81.595 ms
10 2001:1890:c02:f00::115e:4d7d (2001:1890:c02:f00::115e:4d7d) 70.623 ms 67.296 ms 43.987 ms
11 2607:f8b0:8289::1 (2607:f8b0:8289::1) 41.813 ms 85.667 ms 76.925 ms
12 ord38s01-in-x0e.1e100.net (2607:f8b0:4009:811::200e) 78.578 ms 53.261 ms 86.931 ms
```

### Using `tracepath`

The <mark style="color:red;">**`tracepath`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command is **used to trace the path to a network host, discovering MTU** (maximum transmission unit) along the path. The functionality is similar to `traceroute`. **It sends ICMP and UDP messages** of various sizes to find the MTU size on the path.&#x20;

{% hint style="warning" %}
Using UDP messages to trace the path can be **useful when routers are configured to filter ICMP traffic.**
{% endhint %}

To trace the path to a host, execute the following command:

```bash
sysadmin@localhost:~$ tracepath netdevgroup.com
1?:    [LOCALHOST]                    pmtu 1500
⁠⁠ 
1:    192.168.1.1                    2.041ms
1:    192.168.1.1                    1.387ms
2:    172.72.53.1                    2.285ms
3:    sur02.englewood.co.denver.comcast.net        13.123ms
4:    edge3.Denver.Level3.net                14.657ms
5:    car2.Charlotte1.Level3.net            54.875ms
6:    rtp7600-gw-tg4-2-to-trp-crs-gw.ncren.net    58.711ms
7:    dc6500-1-10g.dcs.mcnc.org            59.248ms
13:    no reply
```

Much like the `traceroute6` command, the `tracepath6` command can also be used to determine what route communications travel between local and remote systems.

The <mark style="color:red;">`tracepath`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`tracepath6`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command **use the sockets API to map out paths**, which can be useful when routers are configured to filter out ICMP traffic.

### Using `ethtool`

The <mark style="color:red;">**`ethtool`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> utility is **useful for configuring and troubleshooting network devices such as Ethernet cards** and their device drivers.

```
ethtool [OPTION...] devname
```

In the example below, the <mark style="color:red;">`ethtool`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is used with the <mark style="color:red;">`-i`</mark> or <mark style="color:red;">`--driver`</mark> option to show the driver information for Ethernet device `ens3`

```bash
sysadmin@localhost:~$ sudo ethtool -i ens3 | head -n 20
MAC Registers
-------------
0x00000: CTRL (Device control register)  0x48140240
     Endian mode (buffers):              little
     Link reset:                         normal
     Set link up:                        1
     Invert Loss -Of-Signal:             no
     Receive flow control:               enabled
     Transmit flow control:              disabled
     VLAN mode:                          enabled
     Auto speed detect:                  disabled
     Speed select:                       1000MB/s
     Force speed:                        no
     Force duplex:                       no
0x00008: STATUS (Device status register) 0x80080783
     Duplex:                             full
     Link up:                            link config
   TBI mode:                             disabled
   Link speed:                           1000Mb/s
   Bus type:                             PCI
```

The <mark style="color:red;">`ethtool`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can also be **used to display other useful troubleshooting information**, such as the speed of an interface.

```bash
sysadmin@localhost:~$ sudo ethtool ens3 | grep Speed
             Speed:1000Mb/s
```

### Using `ip neighbor`

One of the most useful things to know when troubleshooting networks is what machines are on the same network segment as you. The <mark style="color:red;">`ip neighbor`</mark> command, part of the <mark style="color:red;">`iproute2`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command suite, is **used to add, change, or replace entries in the ARP (Address Resolution Table) cache tables.**

To **display the ARP cache on a specific interface**, use the <mark style="color:red;">`ip neighbor show`</mark> command with the interface name&#x20;

```
ip [OPTION...] neighbor command
```

The following command will display the contents of the local ARP cache entries (IP addresses that have been resolved to MAC addresses accessible on the network) for the `ens3` network interface.

```bash
root@localhost:~# ip neighbor show dev ens3
10.0.2.2 lladdr 52:55:0a:00:02:02 STALE
10.0.2.3 11addr 52:55:0a:00:02:03 STALE
```

### Using `ip link`&#x20;

The <mark style="color:red;">`ip link`</mark> command ,introduces as part of the <mark style="color:red;">`iproute2`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> tools, replaces the `ifconfig` command. It is **useful for network troubleshooting at the Data-Link (OSI Layer 2) level**.&#x20;

The <mark style="color:red;">`ip link`</mark> command is used to display and manage network interfaces.

```
ip link { COMMAND | help }
```

The <mark style="color:red;">`ip link`</mark> command is executed by itself will display all interfaces on the network and their state:

```bash
root@localhost:~# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_code1 state UP mode DEFAULT group default qlen 1000
    link/ether 52:54:00:12:34:56 brd ff:ff:ff:ff:ff:ff
```

If you are trying to determine the status of previously configured interfaces, the <mark style="color:red;">`ip link show`</mark> command is one tool for doing so. To display information about a specific pre-configured interface, use the <mark style="color:red;">**`ip link show ens3`**</mark> **command**.

```bash
root@localhost:~# ip link show ens3
2: ens3: <LOOPBACK,UP,LOWER_UP> mtu 1500 qdisc fq_code state UP mode DEFAULT group default qlen 1000
link/ether 52:54:00:12:34:56 brd ff:ff:ff:ff:ff:ff
```

Including the <mark style="color:red;">**`-br (--brief)`**</mark> option **only prints basic information** formatted in a tabular output that is easier to read.

```bash
sysadmin@localhost:~$ ip -br link show                                          
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>        
eth0@if39386     UP             02:42:c0:a8:01:02  <BROADCAST,MULTICAST,UP,LOWER_UP>
```

### Using `netcat`

The <mark style="color:red;">`netcat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility it has many features for **monitoring and debugging network connections**. Some **uses are trasferring data, acting as a network proxy, and scanning for open ports**.

```bash
netcat [-options] hostname port[s] [ports] ...
```

The <mark style="color:red;">`netcat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can also be used in the short form, which is <mark style="color:red;">`nc`</mark>.&#x20;

To demonstrate using the `netcat` utility to find ports, the following example will scan for open ports on the local interface `192.168.1.2`, using the <mark style="color:red;">`netcat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command with the <mark style="color:red;">`-z`</mark> option, which **tells it to only scan for open ports** without sending any data to them, as well as with the <mark style="color:red;">`-v`</mark> option for verbose output.

The command below will scan ports 20 through 25 on the `192.168.1.2` interface:

```bash
sysadmin@localhost:~$ netcat -z -v 192.168.1.2 20-25                            
netcat: connect to 192.168.1.2 port 20 (tcp) failed: Connection refused         
netcat: connect to 192.168.1.2 port 21 (tcp) failed: Connection refused         
Connection to 192.168.1.2 22 port [tcp/ssh] succeeded!                          
netcat: connect to 192.168.1.2 port 23 (tcp) failed: Connection refused         
netcat: connect to 192.168.1.2 port 24 (tcp) failed: Connection refused         
netcat: connect to 192.168.1.2 port 25 (tcp) failed: Connection refused
```

Upon examining the results above, we can see that only port 22 (`ssh`) is open on this system.

The <mark style="color:red;">`netcat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can also be **used to create a communication socket between computers**. Given two computers and the knowledge of one of their IP addresses, use the following command to initiate a TCP session on port 23.

```bash
sysadmin@localhost:~$ sudo netcat -l 23
```

With knowledge of the IP address of the first computer, type the following on the second computer:

```bash
sysadmin@localhost:~$ netcat 192.168.1.2 23
```

Now, anything typed on either computer will appear on both

## Troubleshooting Network Interfaces

When troubleshooting a network interface, it is important to know how to verify network connectivity systematically. By using the Open Systems Interconnection (OSI) model as a reference, you will be able to test interface connectivity, network addressing, gateways, routing, DNS, and more.

| Layer          |
| -------------- |
| 7 Application  |
| 6 Presentation |
| 5 Session      |
| 4 Transport    |
| 3 Network      |
| 2 Data-Link    |
| 1 Physical     |

### Physical Layer

The physical layer of the OSI model **defines hardware connections and turns binary data into physicial pulse** (electrical, light or radio waves)

The first questions that an administrator would need to answer when determining network connectivity are. "It the device on?", "Is my network card detected", etc.

{% hint style="warning" %}
No amount of Bash commands can turn the wireless switch on, but you can test for it by using the following command:
{% endhint %}

```bash
sysadmin@localhost:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    
2: enp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 0c:9d:92:60:00:52 brd ff:ff:ff:ff:ff:ff
    
3: enx000ec6a415ca: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
    link/ether 00:0e:c6:a4:15:ca brd ff:ff:ff:ff:ff:ff
```

The highlighted **`NO-CARRIER`** message in the output of the `enx000ec6a415ca` interface **indicates that the interface is not connected to a network**. In some cases, the output may not even contain the interface at all.

```bash
sysadmin@localhost:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    
2: eth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 1000
    link/ether b8:27:eb:b6:76:14 brd ff:ff:ff:ff:ff:ff
    
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DORMANT group default qlen 1000
    link/ether b8:27:eb:e3:23:41 brd ff:ff:ff:ff:ff:ff
```

It is possible that the device does not detect a network card or has not loaded a kernel driver for it. To **verify that a network card is detected**, use the <mark style="color:red;">**`lspci`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command.

```bash
sysadmin@localhost:~$ lspci | grep Ethernet
04:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 15)
```

In the **event that the device does not have a PCI bus**, or there is a USB to Ethernet converter installed, the <mark style="color:red;">**`lsusb`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> and <mark style="color:red;">**`lsmod`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command may be useful

```bash
sysadmin@localhost:~$ lsusb
Bus 003 Device 003: ID 0b95:7720 ASIX Electronics Corp. AX88772
Bus 003 Device 002: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
sysadmin@localhost:~$ lsmod | grep asix
asix                   45056  0
usbnet                 45056  1 asix
mii                    16384  3 r8169,usbnet,asix
```

{% hint style="warning" %}
Wireless devices may not show any network hardware using either <mark style="color:red;">`lspci`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> or <mark style="color:red;">`lsusb`</mark>.
{% endhint %}

{% hint style="info" %}
Although be aware that the <mark style="color:red;">`iwconfig`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`iwlist`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can be used to determine wireless connectivity.
{% endhint %}

### Data-Link Layer

The data-link layer of the OSI model defines the interface to the physical layer. It also **monitors and corrects for errors** in the physical layer **by using a frame check sequence (FCS)**

The **data link layer keeps a table of IP address to MAC address translations**. This is called the address resolution protocol (**ARP**) table. The <mark style="color:red;">**`ip neighbor`**</mark> command displays a list of translations or ARP.

```bash
sysadmin@localhost:~$ ip neighbor
192.168.0.3 dev enp4s0 lladdr 10:3d:0a:47:5b:53 STALE
192.168.0.23 dev enp4s0 lladdr 00:08:22:e2:4d:fb STALE
192.168.0.13 dev enp4s0 lladdr b8:27:eb:e3:23:41 STALE
10.10.10.156 dev enx000ec6a415ca lladdr 00:18:dd:02:01:35 STALE
192.168.0.1 dev enp4s0 lladdr 2c:30:33:90:46:0a REACHABLE
192.168.0.4 dev enp4s0 lladdr 00:18:61:0f:00:d5 STALE
192.168.0.253 dev enp4s0 lladdr b8:27:eb:34:1c:ac REACHABLE
fe80::2e30:33ff:fe90:460a dev enp4s0 lladdr 2c:30:33:90:46:0a router
```

From the output above, and administrator can input the MAC address into the Wireshark OUI tool to determine the manufacturer of the network card. This information can be useful in determining if a particular device is found on the network.

The <mark style="color:red;">`ethtool`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is also **useful for determining connectivity at the data-link layer** along with the link connection speed, duplex, and other details.

```bash
root@localhost:~ ethtool enp4s0 | tail
Cannot get wake-on-lan settings: Operation not permitted
    Link partner advertised FEC modes: Not reported
    Speed: 1000Mb/s
    Duplex: Full
    Port: MII
    PHYAD: 0
    Transceiver: internal
    Auto-negotiation: on
    Current message level: 0x00000033 (51)
                   drv probe ifdown ifup
```

{% hint style="info" %}
The <mark style="color:red;">`arp -a`</mark> command performs a similar function to <mark style="color:red;">`ip neighbor`</mark> ,as does the Windows <mark style="color:red;">`arp`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command
{% endhint %}

## Network Layer

The network layer of the OSI model **performs network routing functions**, **defines logical addresses**, and uses a hierarchical addressing scheme. Various protocols specify packet structure and processing used to carry data from host to host.

Use the <mark style="color:red;">**`ifconfig`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> or <mark style="color:red;">**`ip address`**</mark> command to determine the various addresses assigned to an interface.

```bash
sysadmin@localhost:~$ ip address
2: enp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 0c:9d:92:60:00:52 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.7/24 brd 192.168.0.255 scope global dynamic noprefixroute enp4s0
       valid_lft 61212sec preferred_lft 61212sec
    inet6 2601:401:8001:45c:e9d:92ff:fe60:52/64 scope global dynamic mngtmpaddr
       valid_lft 326281sec preferred_lft 326281sec
    inet6 fe80::e9d:92ff:fe60:52/64 scope link
       valid_lft forever preferred_lft forever
```

From the output above, a network administrator can determine the MAC address of `enp4s0` ,the IPv4 address and subnet mask, and the IPv6 addres and prefix lenght of /64 bits.

{% hint style="info" %}
Be aware that the <mark style="color:red;">**`dhclient`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command **requests an IP address from a DHCP server**, akin to the **Windows** <mark style="color:red;">`ipconfig \renew`</mark> command.

To use the command, an administrator requires `sudo` access.

```
root@localhost:~# dhclient enp4s0 -v
Internet Systems Consortium DHCP Client 4.3.5
Copyright 2004-2016 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/
Listening on LPF/enp4s0/0c:9d:92:60:00:52
Sending on   LPF/enp4s0/0c:9d:92:60:00:52
Sending on   Socket/fallback
DHCPREQUEST of 192.168.0.7 on enp4s0 to 255.255.255.255 port 67 (xid=0x372c640d)
DHCPACK of 192.168.0.7 from 192.168.0.1
RTNETLINK answers: File exists
bound to 192.168.0.7 -- renewal in 42030 seconds.
```
{% endhint %}

### Routing Table Testing

When checking network connectivity, ensure that your system can get to the assigned gateway. The network gateway, as defined om your network interface configuration, is the "first hop" or the first place your computer will go to when looking for resources beyond the local network. Use the `ping` command to determine connectivity to the gateway IP:





