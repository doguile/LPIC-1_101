# Module 5: Networking

## Chapter 13: Network Fundamentals

> **109.1 : Fundamentals of internet protocols v2**
>
> Weight: 4

<details>

<summary>Key terms</summary>

`/etc/services` File used to map applications service names to port numbers and can be modified only by the root user.

`IPv4, IPv6` The IPv4 addresses are made up of four 8-bit octests for a total of 32-bits, whereas the IPv6 addresses are based on 128-bits

`Subnetting` A subnet is either an entire class A,B, or C network or a portion of one of these networks.

`TCP, UDP, ICMP` Three communicatoin protocols that make up the Internet Protocol Suite.

* TCP (Transmission Control Protocol) provides connection-oriented service between two application exchanging data and guarantees delivery of data. (i.e ftp and telnet are two commands that use TCP for transport).
* UDP (User Datagram Protocol) provides connection-less service between two application exchaging without guaranteeing the transfer of data. UDP is faster than TCP and is used in services such as VoIP, Straming Video, and DNS
* ICMP (Internet Control Message Protocol) Is a diagnostic protocol used to notify about network problems which are causing delivery failures.

`dig` The dig (Domain Informatin Groper) command is used for troubleshooting the configuration of DNS servers.

`host` The host command is used to resolve hostnames to IP addresses and IP addressses to hostnames.

`route` Command used to view and update the IP routing table.

</details>

## Introduction

Providing access to the network is the responsibility of the root user on the system. Unfortunately, this can sometimes be a difficult task. Network cards are not all standardized, so additional configuration may be required.

In addition, there are several components that need to be configured for the system to connect to the network, including IP address settings, gateway settings, and DNS settings.

## Network Basics

Every computer and device on a network has a unique identifier. Most network today, including all computers on the internet, use the _TCP/IP_ protocol developed by the _Internet Engineering Task Force (IETF)_ as the standard for how to communicate on the network.

In the TCP/IP model, the **unique identifier for a computer is its IP address.** An IP address can be either _static_, which is assigned manually, or _dynamic_, which is assigned by the _Dynamic Host Configuration Protocol_ (DHCP) running as a service on the network.

**A routing table is a small in-memory database managed by every device on the network.** It is used to **calculate the optimal journey through other routers** in the same or other networks for messages it is responsible for forwarding to a destination address.

TCP/IP is a combination of the two most popular protocols. The **TCP** _(Transmission Control Protocol)_ protocol is **used for reliable delivery of data** between computers connected through a LAN or the internet, and the **IP** _(Internet Protocol)_ protocol is **mainly responsible for the routing of data packets** to the destination address.

{% hint style="info" %}
**Consider this**

The OSI (Open Systems Interconnection) reference model is a conceptual model created by ISO (International Organization for Standards). It encompasses and references many different protocols and standards to achieve this and has proven to be very successful for designing and troubleshooting computer networks.

The OSI model is comprised of seven different conceptual layers, each responsible for a different function within the network and each with its own protocol data unit (PDU), which represents the unit of data handled at that layer.
{% endhint %}

The following is a summary of the layer in the OSI Model:

| Layer        | Purpose                                                                  |
| ------------ | ------------------------------------------------------------------------ |
| Application  | User interface, API                                                      |
| Presentation | Data presentation, encryption                                            |
| Session      | Controls connection between hosts, maintains ports and session           |
| Transport    | User transmission protocols to transmit data (TCP, UDP)                  |
| Network      | Determines path of data, IP                                              |
| Data Link    | Physical addressing (MAC), delivery of frames (Protocol Data Unit (PDU)) |
| Physical     | Transmits raw data between physical media                                |

## IPv4 Addresses

The IP address is used to identify the network interface of a device on the network. Each computer on a network is assigned a unique IP address. The IP address is a numerical identifier in _decimal dotted quad_ format since there are four values in an IPv4 address (`192.168.0.1`).&#x20;

> A server or PC can support more than one network interface, and each of the interfaces can be assigned a distinct IP address.

The IP address is made up of 4 octets, which are sets of 8-bits values. The value of each of these octets can range from decimal values `0 - 255` ( or in binary, `00000000 - 11111111`)

```bash
00001010. 00001001. 00001000. 00000001
|______|  |______|  |______|  |______|  
    |         |         |         |
Octet #1   Octet #2   Octet #3  Octet #4
```

{% hint style="warning" %}
**Note**

The 8-bit binary format can be translated to decimal value by using a multiplier. Only bits set to `1` will use a multiplier, and the multiplier is based on the location of the bit in the octet. Bits set to `1` should be assigned a value of 2, and bits set to `0` should be assigned a value of 0. The following shows what multipliers to use, based on the location of the bit:

<img src="../.gitbook/assets/imagen (3).png" alt="" data-size="original">

To demonstrate, based on the information above, we can determine by adding the multiplied bits (8+2) that the octet `00001010` has a decimal value of 10:

![](<../.gitbook/assets/imagen (2).png>)
{% endhint %}

An IPv4 network addressing scheme has been designed on the basis of the octects. It classifies networks into 5 classes: A,B,C,D and ,E.

* **Class A -** The **network is denoted by the first octect**, and the r**emaining three octets are used to create subnets** or identify hosts on the network. The first bit of the first octet is always `0` , so the range of values permissible is `00000001 - 01111111` ,i.e., `1 - 127` in decimal value (the first number of an IP address cannot be `0` by definition of IP addresses).  <mark style="color:red;">`1.0.0.0 - 126.0.0.0`</mark>
* **Class B -** The **network is denoted by the 1st and 2nd octets**, and the **remaining 2 octets are used to create subnets** or identify hosts. The range values permissible are <mark style="color:red;">`128.0.0.0 - 191.255.0.0`</mark>&#x20;
* **Class C -** The **network is denoted by the 1st, 2nd and 3rd octets**, and **the last octet is used to create subnets** or identify hosts.The range values permissible are <mark style="color:red;">`192.0.0.0 - 223.255.255.0`</mark>&#x20;
* **Class D -** These addresses are **not assigned to network interfaces** and are **used for multicast operations** such as audio-video streaming. The 1st, 2nd, 3rd, and 4th bits of the first octet are set to `1, 1, 1,` and `0` respectively. The range values permissible are <mark style="color:red;">`224.0.0.0 - 239.255.255.255`</mark>&#x20;
* **Class E -** These addresses are reserved for future use and the ranges values are <mark style="color:red;">`240.0.0.0 - 255.255.255.255`</mark>

| Class   | Range                       |
| ------- | --------------------------- |
| Class A | `1.0.0.0 - 126.0.0.0`       |
| Class B | `128.0.0.0 - 191.255.0.0`   |
| Class C | `192.0.0.0 - 223.255.255.0` |
| Class D | `224.0.0.0 - 239.0.0.0`     |
| Class E | `240.0.0.0 - 255.0.0.0`     |

## Understanding Network Masks

In order for computers to communicate directly on the same network, all the computers must be on the same subnet. A subnet is either an entire class A,B or C network or a portion of one of these networks.

> To take a large class network and create a smaller portion, use a subnet mask.

The **subnet mask is used to differentiate the network and subnet components of the IP address**. The subnet mask is not an IP address in itself; it is a numeric pattern used to indicate the portion of the IP addres that contains the network identifier.

The addresses for Class A, B and C have default mask as follows:

| Network Class | Subnet Mask     |
| ------------- | --------------- |
| Class A       | `255.0.0.0`     |
| Class B       | `255.255.0.0`   |
| Class C       | `255.255.255.0` |

For example, consider a standard Class A IP address `10.9.8.1` and its default subnet mask expressed in binary format:

* IP address: `00001010. 00001001. 00001000. 00000001`
* Subnet mask: `11111111. 0000000. 00000000. 00000000`

In the subnet mask, the octets where the **mask bits are `1`** represent the **network ID**, whereas the octets where the **mask bits are `0`** represent the **host ID**

```bash
11111111. 00000000. 00000000. 00000000
|______|  |__________________________|  
   |                    |
Network ID           Host ID
```

In the previous example IP Address `10.9.8.1`, the network ID is `10` and the host ID is `9.8.1`.

For example of a custom subnet, assume that a class C network, `202.16.8.0` has been allocated. In this case, you would take two bits from its default subnet mask and replace them with `1s` as follows:

* Default Mask: `11111111. 11111111. 11111111. 00000000`
* Subnet Mask: `11111111. 11111111. 11111111. 11000000`

Using the two bits will give 4 (2^2) subnets, the remaining 6 bits will give 64 (2^6) host addresses for each subnet. The address range will be as follows:

* `202.16.8.0 - 202.16.8.64`
* `202.16.8.65 - 202.16.8.128`
* `202.16.8.129 - 202.16.8.192`
* `202.16.8.193 - 202.16.8.224`

The subnet mask `255.255.255.192` has partitioned the class C network address into 4 sub-networks, and each of these sub-networks can be assigned to a particular group of machines.

## Public and Private IPv4 Adresses

There are two types of IP addresses used on a network: _public_ and _private_. The **InterNIC (Network Information Center)** is the global body responsible for assigning public addresses. They assign class-based network IPs, which are always unique. The public addresses are available with internet routers so that data can be delivered correctly

{% hint style="info" %}
Only systems such as email servers, web servers, and proxies (which hanlde intermediary request from clients seeking resources from other servers) need direct connectivity to the internet so that users outside of the LAN can connect to these servers.
{% endhint %}

On the other hand, users who work on their own machines and want to connect to the internet do not need a globally unique IP address. Instead, they can be assigned private IP addresses, which are then converted to public IP addresses by the gateway/router.

According to **RFC 1918**, a portion of the IP address space has been designated as "_private addresses_". This range of addresses does not overlap with the public addresses.

> The private addresses can be reused, which means the risk of running out of addresses on the internet has been mitigated. The private address space is not directly reachable through the internet.

To access devices utilizing the private address space, a router using NAT (Network Address Translation) will need to be configured. There are 3 blocks of private addresses:

**Class A  -** This range address allow from `10.0.0.1 - 10.255.255.254`. The 24 bits from the host ID are available for subnetting

```
10.0.0.0/8 - 255.0.0.0
```

**Class B -** network allows the range of addresses from `127.16.0.1 - 172.31.255.254` .The 20 bits from the host ID are available for subnetting.

```
172.16.0.0/12 - 255.240.0.0
```

Class C - network allows the range of addresses from `192.168.0.1 - 192.168.255.254`. The 16 bits from the host ID are available for subnetting.

```
192.168.0.0/16 - 255.255.0.0
```

## Comparing IPv4 and IPv6

The IPv4 addresses are made up of four 8-bit octets for a total of 32-bit. This means the maximum numbner of possible addresses is 2^32, which is less than 4,294,967,296.

The IPv6 addresses are based on 128-bit. Using similar calculations,as shown above, the maximum number of possible addresses is 2^128, which gives a massively large pool of addresses.

{% hint style="info" %}
The IPv6 addresses consist of eight 16-bit segments, which means each segment can have 2^16 possible values, and are expressed in hexadecimal format.
{% endhint %}

A brief comparison of IPv4 vs IPv6

| Feature             | IPv4                                                       | IPv6                                                                                      |
| ------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Address Size        | 32-bit                                                     | 128-bit                                                                                   |
| Address Format      | <p>Decimal dotted quad</p><p><code>192.168.20.8</code></p> | <p>Hex notation</p><p><code>4AAE:F200:0342:AA00:0135:4680:7901:ABCD</code></p>            |
| Number of addresses | 232                                                        | 2128                                                                                      |
| Broadcasting        | Uses broadcasting to send data to all hosts on a subnet    | No broadcast addresses, uses multicast scoped addresses as a way to selectively broadcast |

## Default Route

The function of routing is to send and IP packet, consisting of a header (source and destination address) and encapsulated data, from one point to another.

All devices have **routing tables**, which contain routes **used to calculate the optimal journey of hte messages** that it is responsible for forwarding through other routers in the same or other networks.

When a computer sends packets to another computer, it consults its routing table. **If a packet is being sent to a destination on the same subnet, no routing is needed**, and the packet is sent directly to the computer. **If a packet is being sent to another network**, **then the first "hop" a packet will go** **is to whatever is in the default gateway field** and lets the router decide the optimal path forward.

The router for the network will have its own routing table, including its own _default route_. The routing table is a list of other routers that are connected to the current router. If the router receives a packet for a network destination that it has in its routing table, it simply forwards it. Otherwise, **the router will send the packet to its default route**.

To view the existing routing table, execute the `route` command:

```bash
root@localhost:~# route                                                  
Kernel IP routing table
Destination       Gateway       Genmask         Flags Metric Ref Use Iface
default           192.168.1.1     0.0.0.0       UG    0       0   0   eth1
192.168.1.0       *           255.255.255.0     UG    0       0   0   eth1
192.168.2.0       *           255.255.255.0     UG    0       0   0   eth0
```

In the output of the kernel routing table:

* The first column contains the `Destination` network addresses. The word `default` signifies the default route.
* The second column contains the defined `Gateway` for the specified destination. In the event that **an asterisk **<mark style="color:red;">**`*`**</mark>** is shown**, it means that **a gateway is not needed to access the destination network**.
* The `Genmask` column shows the netmask for the destination network.
* In the `Flags` column, **a **<mark style="color:red;">**`U`**</mark>** means the route is up and available**, whereas the <mark style="color:red;">**`G`**</mark>** means that the specified gateway should be used for this route**.
* The <mark style="color:red;">**`Metric`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> column defines the **distance to the destination**. This is typically listed in the **number of hops** (the number of routers between source and destination)
* The `Ref` column is not used by the Linux Kernel.
* The <mark style="color:red;">**`Use`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> column is used to define the **number of lookups for the route**.
* Finally, the `Iface` column is used to define the exit interface for this route.

All other network packets are sent to the router with the IP address of 192.168.1.1 via the `eth1` network card.

```bash
route add default gw 192.168.1.1 eht1
```

Now , if any of the routes in the routing table do not match the specified address, then the packet will be forwarded to `192.168.1.1` (the default route)

{% hint style="info" %}
The <mark style="color:red;">`route`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command has been superseded by the <mark style="color:red;">`ip route show`</mark> command ,which is part of the <mark style="color:red;">`iproute2`</mark> suite of utilities.
{% endhint %}

{% hint style="info" %}
New routes can be added on-the-fly with the <mark style="color:red;">`ip route add`</mark> command, but they are not persistent. To add a router device that has the IP address of `10.0.0.0` as a new default route, execute the following command.&#x20;

```bash
sudo ip route add 10.0.0.0/24 dev ens3
ip route show
```


{% endhint %}

## Understanding TCP

The _Transmission Control Protocol (TCP)_ provides **connection-oriented service** between two applications exchanging data. The protocol **guarantees delivery of data**.

The **sequence number** mechanism in the header **ensures ordered delivery of data**. The web server will then service GET requests sent on the HTTP port for web pages. **For error control**, **TCP uses the acknowledgment number** in the header.

> The client sends the acknowledgment number to the server. If the server sends 2000 bytes of data to the client and the client acknoledges only 1000 bytes, then it indicates loss of data. The web server will thne retransmit the data.

## Using FTP

FTP is a protocol that **uses TCP for transport** and reliable delivery. The <mark style="color:red;">`ftp`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command provides the user interface to the standard File Transfer Protocol (FTP). Using the <mark style="color:red;">`ftp`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility, **a user can transfer files to and from remote machines**. It can be used for UNIX as well as non-UNIX machines. The service is **provided by the **<mark style="color:red;">**`ftpd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon**, which uses the TCP protocol and listens to the FTP ports specified in the `/etc/services` file for FTP requests.

To connect to a particular host via `ftp`, execute the command:

```bash
ftp ftp_server_host_name [or IP address]
```

For example:

```bash
sysadmin@localhost:~$ ftp 127.0.0.1
ftp>
```

If the FTP service is running on the specified host, the user will be prompted to log in using a known user name and password.

{% hint style="danger" %}
**Consider this**

The username and password used to log in to an FTP server are sent over the network without being encrypted. The opportunity for someone to capture this information as it crosses from network to network and host to host is very dangerous.
{% endhint %}

Once logged into the FTP server, the `ftp>` prompt will be displayed. You will find that you can view and navigate the filesystem of the FTP server similar to your own local filesystem using command like `ls` and `cd` commands.

The <mark style="color:red;">`lcd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can be **used to change your local working directory** to affect where files will get downloaded or uploaded.

```bash
ftp>  lcd                                         
Local Directory now /home/sysadmin                                               
ftp>  lcd  /tmp                                        
Local Directory now /tmp
```

To **execute other commands** on your local machine while logged in to the FTP server, prefix a command with **an **<mark style="color:red;">**`!`**</mark>** exclamation point**.

```bash
ftp> !ping 127.0.0.1                                        
PING 127.0.0.1[127.0.0.1]  56(84)  bytes of data.
64 bytes from 127.0.0.1:  icmp_seq=0  ttl=255  time=0.5 ms
64 bytes from 127.0.0.1:  icmp_seq=1  ttl=255  time=0.3 ms
 
‌⁠​​⁠​ 
---  127.0.0.1  ping statistics ---
2 packets transmitted, 2 packets received,  0% packet loss round-trip
```

For additional assistance, use the <mark style="color:red;">`help`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command while logged in to the FTP server.

```bash
ftp> help
ftp> ?
```

The **default file transfer mode for the** <mark style="color:red;">`ftp`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> **utility is **_**ASCII**_, which is used for ordinary text files. To transfer other types of files (i.e program files, zip files, or tar files, etc.) it is recommended that the server is in **binary transfer mode**. To set the file transfer mode to binary, execute the <mark style="color:red;">`bin`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command in the FTP server.

```bash
ftp> bin
```

To **retrieve or download a file from the remote machine** to your local machine, **use the **<mark style="color:red;">**`get`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command**. For example, to download the `devsrcode.tar` file (binary) from the FTP server named `server1` to the `/tmp` directory on the local machine, execute the following `ftp` commands:

```bash
sysadmin@localhost:~$ ftp server1
ftp> bin
ftp> lcd /tmp
Local Directory no /tmp
ftp> get devsrcode.tar
local: devsrccode.tar remote: devsrccode.tar
200 PORT command successful.  Consider using PASV.
150 Opening BINARY mode data connection for devsrccode.tar (1000 bytes).
226 Transfer complete.
ftp> !ls
devsrcode.tar
```

To **send or upload a file from your local machine** to the remote machine, **use the **<mark style="color:red;">**`put`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command**. For example, to upload the `devrscode.tar` from the local machine to the server, execute the following command:

```bash
ftp> put devsrcode.tar
```

To download multiple files, use the `mget` command. If you wanted to download all the `*.tar` files in a particular directory from the server to the local machine, execute the following command:

```bash
ftp> mget *.tar
```

To quit <mark style="color:red;">`ftp`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ,execute the command:

```bash
ftp> quit
sysadmin@localhost:~$
```

## Using Telnet

Telnet is another protocol that **uses TCP for transport and reliable delivery**. The telnet Protocol is **used for interactive communication** with host machines using TCP/IP. The <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command provides the user interface to the standard Telnet protocol. T**he service is provided by the `telnetd` daemon,** which services requests from clients to connect to the Telnet port specified in the `/etc/services` file.

To open a <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> session to the server, execute the following:

```
telnet host_name [or IP address]
```

If either the hostname or IP address is specified, then the <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command implicity executes the `open` option to open a connection to the host. If the telnet service is running on the specified host, then the user will be prompted to log in using their user name and password.

```bash
sysadmin@localhost:~$ telnet server1
Trying 192.9.49.10 …
Connected to server1
Escape character is ‘^]’.
Welcome to server1
 
‌⁠​​⁠​ 
login: root
Password:
Last login:  Mon Mar 8 8:35:15 from localhost
root@localhost:~#
```

To end a <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> session, log out normally with the <mark style="color:red;">`logout`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> or <mark style="color:red;">`exit`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command. **If you find yourself stuck within the **<mark style="color:red;">**`telnet`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** client**, use **Ctrl+]** to get to a <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> prompt. From the <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> prompt, type `help` to get assisstance or `quit` to exit `telnet`

The `telnet` protocol can be very **useful as a troubleshooting tool to determine whether a daemon is listening and responding on a certain port**. To specify a port other than the standard port 23, you add the port number after the host specification. For example, to see if there is a service or daemon listening on port 25 on your own host, execute:

```bash
root@localhost:~# telnet localhost 25                                           
Trying ::1...                                                                   
Trying 127.0.0.1...                                                             
Connected to localhost.                                                         
Escape character is '^]'.                                                       
220 localhost ESMTP Sendmail 8.14.4/8.14.4/Debian-4.1ubuntu1; Mon, 8 Mar 2015 
8:40:15 GMT; (No UCE/UBE) logging access from: localhost(OK)-localhost [127.0.0.
1]                                                                              
^C
```

{% hint style="info" %}
**Consider this**

The <mark style="color:red;">`telnet`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> protocol suffers the same defect as <mark style="color:red;">`ftp`</mark>; the packets that are sent aare not encrypted.
{% endhint %}

## Understanding UDP

_User Datagram Protocol (UDP)_ provides **connectionless service** between two applications exchanging data. Unlike TCP, **UDP has no error control** and does not guarantee the transfer of data.

UDP **sends data without notifying the receiver prior to sending**. As a result, it does not offer either ordered or reliable delivery.

The header of UDP packets is lightweight as compared to TCP packets since it **does not contain sequence or acknowledgment numbers**. It uses a simple, optional checksum mechanism for error-checking.

**UDP is faster than TCP** and is used in services such as VoIP, streaming video, and DNS.

## `/etc/services` File

In order to make it easy to distinguish between packets destined for different services, each service is assigned one or more port numbers.

The `/etc/services` file is used for mapping application service names to port numbers. This file exists on each system and can be modified only by the root user. Generally, there is no need to modify this file since most of the service use their own configuration files to determine port numbers; however, this file does provide a good reference for standard port numbers.

{% hint style="info" %}
**Consider this**

**The **<mark style="color:orange;">**`/etc/services`**</mark>** file is queried by programs using the **<mark style="color:orange;">**`getportbyname()`**</mark>** ** Application program interface (API) to determine the port number that they should open a socket connection to.&#x20;

For example, if the <mark style="color:red;">`finger`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is used to do a name lookup for a user on a remote machine, then it executes a `getporbyname()` API for the <mark style="color:red;">`finger`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> service and fetches the corresponding port number, which is 79.
{% endhint %}

The port numbers in the range `0 - 1023` are assigned as per the Request For Commnets (RFC) 1700 standard. This RFC also mentions the standard use of ports beyond `1023`

The mapping of some of the commonly-used TCP and UDP ports are:

| Port Number | Service        |
| ----------- | -------------- |
| `20/tcp`    | `ftp-data`     |
| `21/tcp`    | `ftp`          |
| `21/udp`    | `ftp`          |
| `22/tcp`    | `ssh`          |
| `22/udp`    | `ssh`          |
| `23/tcp`    | `telnet`       |
| `25/tcp`    | `smtp`         |
| `53/tcp`    | `domain (dns)` |
| `53/udp`    | `domain (dns)` |
| `80/tcp`    | `http`         |
| `80/udp`    | `http`         |
| `110/tcp`   | `pop3`         |
| `110/udp`   | `pop3`         |
| `119/tcp`   | `nntp`         |
| `139/tcp`   | `netbios-ssn`  |
| `139/udp`   | `netbios-ssn`  |
| `143/tcp`   | `imap2`        |
| `143/udp`   | `imap2`        |
| `161/tcp`   | `snmp`         |
| `161/udp`   | `snmp`         |
| `443/tcp`   | `https`        |
| `443/udp`   | `https`        |
| `465/tcp`   | `ssmtp`        |
| `993/tcp`   | `imaps`        |
| `993/udp`   | `imaps`        |
| `995/tcp`   | `pop3s`        |
| `995/udp`   | `pop3s`        |

## Queying DNS Servers

The `host` and `dig` command are used for DNS (Domain Name System) lookups. A DNS server provides hostname to IP address translation.

Although it has been deprecated for a long time, there is also a command called `nslookup` .Many Linux systems still have the `nslookup` command, but there has been no development for this tool for a long time.

### `host` Command

To find the IP address of a host, execute the `host` command:as

```bash
sysadmin@localhost:~$  host netdevgroup.com
netdevgroup.com has address 152.46.6.105
```

The `host` command also does reverse lookups of IP addresses to names by executing the same syntax:

```bash
sysadmin@localhost:~$  host 152.46.6.105
netdevgroup.com has address netdevgroup.com
```

To **find the DNS servers for a domain**, do not specify the host and **use the **<mark style="color:red;">**`-t`**</mark>** option with an argument of **<mark style="color:red;">**`ns`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ,and execute the command:

```bash
sysadmin@localhost:~$  host -t ns netdevgroup.com
netdevgroup.com name server ns-1328.awsdns-38.org
netdevgroup.com name server ns-1635.awsdns-12.co.uk.
netdevgroup.com name server ns-461.awsdns-57.com
netdevgroup.com name server ns-703.awsdns-23.net
```

### `dig` Command

The `dig` (Domain Information Groper) command is used for troubleshooting the configuration of DNS servers. DNS server administrators like the output of `dig` command because it is in the same format that the information is entered into a DNS server configuration file.

The utility performs DNS lookups and displays the responses received from the name servers listed in the `/etc/resolv.conf` file.

To perform a simple lookup of a hostname, execute the following:

```bash
sysadmin@localhost:~$  dig example.com
; <<>> DiG 9.9.5-3ubuntu0.1-Ubuntu <<>> example.com                             
;; global options: +cmd                                                         
;; Got answer:                                                                  
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 14876                       
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1          
.. OUTPUT OMITTED ...                                        
;; ANSWER SECTION:                                                              
example.com.            86400   IN      A       192.168.1.2                     
;; AUTHORITY SECTION:                                                           
example.com.            86400   IN      NS      example.com. 
;; Query time: 0 msec                                                           
;; SERVER: 127.0.0.1#53(127.0.0.1)                                              
;; WHEN: Wed Mar 11 15:23:13 UTC 2015                                           
;; MSG SIZE  rcvd: 70
```

To view the trace of domain name servers from the servers where the lookup begins, and each name server along the way, execute the `+trace` option to the `dig` command:

```bash
sysadmin@localhost:~$  dig +trace example.com
```

For a reverse lookup, using an IP address instead of a hostname, execute the `-x` option:

```bash
sysadmin@localhost:~$  dig -x 192.168.1.2
```

Some of the key options of this command are:

|     Option    | Meaning                                                                               |
| :-----------: | ------------------------------------------------------------------------------------- |
| `-f filename` | Operate in batch mode by reading a list of lookups to be done from the specified file |
|   `-p port#`  | Query the specified port other than the standard DNS port                             |
|      `-4`     | Use IPv4 query transport                                                              |
|      `-6`     | Use IPv6 query transport                                                              |

## Understanding ICMP

The TCP protocol provides an error control mechanism but does not contain information about possible reasons for errors. The Internet Control Message Protocol (ICMP) is a diagnostic protocol used to notify about network problems that are causing delivery failures. This protocol is considered as a part of the IP protocol, though it is processed differently tna normal IP packets.

Some of the common types of ICMP messages are:

* Destination unreachable
* Redirect
* Time exceeded
* Source quench (i.e host or router is congested)
* Echo Reply/Request (i.e the `ping` command)

A summary of the key features and differences of TCP, UDP, IP and ICMP is shown below.

|                          | TCP                 | UDP              | IP                                    | ICMP                     |
| ------------------------ | ------------------- | ---------------- | ------------------------------------- | ------------------------ |
| **OSI Networking Layer** | Transport           | Transport        | Internet                              | Internet                 |
| **Packet Header Size**   | 20 bytes            | 8 bytes          | Minimum 20 bytes                      | 8 bytes                  |
| **Unit of Data**         | Segment             | UDP Datagram     | IP Datagram                           | Messages                 |
| **Contents**             | Application Data    | Application Data | Encapsulates TCP/UDP application data | Query and Error Messages |
| **Type of Connection**   | Connection-Oriented | Connectionless   | Connectionless                        | Connectionless           |
