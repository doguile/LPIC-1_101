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

## Using `netstat`

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

## Using `ss`

The <mark style="color:red;">`ss`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is designed to **show socket statistics** and supports all the major packet and socket types.

A _network socket_ is a communication endpoint between nodes (devices) on a network. **Sockets use a socket address to receive incoming network traffic and forward it to a process on a machine or service.**

{% hint style="success" %}
The socket address commonly **consists of the IP address of the node** that it is "attached" to **and a port number.**
{% endhint %}

The main reason a user would use the `ss` command is to view what connections are currently established between their local machines and remote machines, statistics about those connections, etc. To use the `ss` command, follow the syntax below:

```bash
ss [options] [FILTER]
```

Similar to the `netstat` command, you can get a great deal of useful information from the `ss` command just by itself, as shown in the example below:

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

The output is very similar to the output of the `netstat` command with no options. The columns above are:

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

## Using `ip`&#x20;

The `ifconfig` command is becoming obsolete in some Linux distributions and is being replacedd with a form of the `ip` command, specially `ip address` .

The format for the `ip` command is as follows:

```
ip [OPTIONS] OBJECT COMMAND
```

While the `ifconfig` is limited primarly to the modification of networking parameters, and displaying the configuration details of networking components, the `ip` command branches out to do some of the work of several other legacy command such as `route` and `arp`.

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

Another useful option with the `ip` command is the `-statistics` or `-s` option, which shows statistics for the object referenced in the command. For example, to show statistics for active IP addresses, you can type:

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
Multiple dropped packets could indicate a Layer 1 issue where network cabling could be the problem.
{% endhint %}









