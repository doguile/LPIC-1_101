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































