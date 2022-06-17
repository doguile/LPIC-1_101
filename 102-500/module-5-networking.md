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
The <mark style="color:red;">`route`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command has been superseded by the <mark style="color:red;">`ip route`</mark> command ,which is part of the <mark style="color:red;">`iproute2`</mark> suite of utilities.
{% endhint %}

## Understanding TCP

The _Transmission Control Protocol (TCP)_ provides **connection-oriented service** between two applications exchanging data. The protocol **guarantees delivery of data**.

The **sequence number** mechanism in the header **ensures ordered delivery of data**. The web server will then service GET requests sent on the HTTP port for web pages. **For error control**, **TCP uses the acknowledgment number** in the header.

> The client sends the acknowledgment number to the server. If the server sends 2000 bytes of data to the client and the client acknoledges only 1000 bytes, then it indicates loss of data. The web server will thne retransmit the data.

## Using FTP







