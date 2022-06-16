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





































