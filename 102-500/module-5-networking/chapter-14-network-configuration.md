---
description: '109.2: Persistent network configuration v2  Weight: 4'
---

# Chapter 14: Network Configuration

<details>

<summary>Key terms</summary>

`/etc/hostname` System file containing the name of the current host

`/etc/hosts` System file used to translate hostnames to an IP addresses.

`/etc/nsswitch.conf` The name service switch file that contains information about which source the resolver should use to look up for information. This file is used for configuring the lookup for different categories of information such as user name, group names ,host names and network names. This file also defines the order of preference of different sources.

`/etc/resolv.conf` The configuration file for DNS resolvers

`hostname` The hostname command is used to set and view the system's host and domain name.

`hostnamectl` Used to query and set system hostnames, but the hostnamectl command provides additional categories for hostnames; static, pretty, and transient

`ifconfig` Command used to view and configure network interfaces

`nmcli` NetworkManager uses the network management command line interface nmcli to manage network connections and display information about devices on a network&#x20;

</details>

## Introduction

The system administrator needs to know how to configure the system for network access.

## TCP/IP Configuration

Recall that the _**Transmission Control Protocol (TCP)**_** is a connection-oriented** service between two applications exchanging data. To configure a network port, or interface, on legacy systems, use the <mark style="color:red;">`ifconfig`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command.

> The <mark style="color:red;">`ifconfig`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command stands for interface configuration and is used to display network configuration information

This command is used for the following functions:

* Assigning static IP address
* Viewing current network configuration
* Setting the netmask
* Setting the broadcast address
* Enable/disable network interfaces

The <mark style="color:red;">`ifconfig`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can be used without options or arguments to display all interfaces on the network, or with options and an interface name as an argument.

```bash
ifconfig [INTERFACE] [OPTIONS]
```

```bash
root@localhost:~ ifconfig                                     
eth0      Link encap:Ethernet  HWaddr b6:84:ab:e9:8f:0a    
          inet addr:192.168.1.2  Bcast:0.0.0.0  Mask:255.255.255.0  
          inet6 addr: fe80::b484:abff:fee9:8f0a/64 Scope:Link       
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1        
          RX packets:95 errors:0 dropped:4 overruns:0 frame:0       
          TX packets:9 errors:0 dropped:0 overruns:0 carrier:0      
          collisions:0 txqueuelen:1000                              
          RX bytes:25306 (25.3 KB)  TX bytes:690 (690.0 B)          
lo        Link encap:Local Loopback                               
          inet addr:127.0.0.1  Mask:255.0.0.0                       
          inet6 addr: ::1/128 Scope:Host                           
          UP LOOPBACK RUNNING  MTU:65536  Metric:1                  
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0        
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0      
          collisions:0 txqueuelen:0                                 
          RX bytes:460 (460.0 B)  TX bytes:460 (460.0 B)
```

{% hint style="info" %}
The `lo` device is referred to as the loopback device. It is a special network device used by the system when sending network-based data to itself.
{% endhint %}

To view the details of a specific interface, execute the following command:

```bash
sysadmin@localhost:~$ ifconfig eht0                                  
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1450            
        inet 192.168.1.2  netmask 255.255.255.0  broadcast 0.0.0.0              
        inet6 fe80::42:c0ff:fea8:102  prefixlen 64  scopeid 0x20<link>          
        ether 02:42:c0:a8:01:02  txqueuelen 0  (Ethernet)                       
        RX packets 13  bytes 1038 (1.0 KB)                                      
        RX errors 0  dropped 0  overruns 0  frame 0                             
        TX packets 8  bytes 648 (648.0 B)                                       
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

To assign an IP address to an interface, execute the following:

```bash
sudo ifconfig eth0 192.168.1.3
```

To assign a netmask to an interface, execute the following:

```bash
sudo ifconfig eth0 netmask 255.255.255.192
```

To assign a broadcast address to an interface, execute the following:

```bash
sudo ifconfig eth0 broadcast 192.168.1.63
```

The `ifconfig` command can be used to enable and disable an interface. To disable (deactivate) a network interface, execute the following command:

```bash
sudo ifconfig eht0 down
```

To view the changes that were made, execute the following:

```bash
sysadmin@localhost:~$ ifconfig eth0                                 
eth0: flags=4163<BROADCAST,MULTICAST>  mtu 1450                      
        inet 192.168.1.2  netmask 255.255.255.0  broadcast 0.0.0.0              
        inet6 fe80::42:c0ff:fea8:102  prefixlen 64  scopeid 0x20<link>          
        ether 02:42:c0:a8:01:02  txqueuelen 0  (Ethernet)                       
        RX packets 13  bytes 1038 (1.0 KB)                                      
        RX errors 0  dropped 0  overruns 0  frame 0                             
        TX packets 8  bytes 648 (648.0 B)                                       
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Note from the output above that the `eth0` interface is no longer `UP` and `RUNNING` .

To enable (activate) a network interface, execute the following:

```bash
sudo ifconfig eth0 up
```

{% hint style="warning" %}
The <mark style="color:red;">`ifconfig`</mark>command can also be used to modify network settings temporarily. Typically these changes should be permanent.
{% endhint %}

### Setting the Hostname

The hostname is used to identify the system by applications such as web servers.

> On Debian-derived and modern Red Hat-derived systems, the `/etc/hostname` file contains this information, while legacy Red Hat-derived systems store this information in the `/etc/sysconfig/network` file. This file is read at boot time to set the hostname

The <mark style="color:red;">`hostname`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is used to set and view the system's host and domain name. To view the currently assigned hostname of the system, execute the `hostname` or short name cut at the first dot <mark style="color:red;">`hostname -s`</mark>, or full domain name <mark style="color:red;">`hostname -f`</mark> command:

```bash
sysadmin@localhost:~$ hostname -s
localhost

sysadmin@localhost:~$ hostname -f
test.example.com
```

> The name displayed above is returned by the `gethostname()` application programming interface (API)

{% hint style="info" %}
Note that setting the hostname using the <mark style="color:red;">`hostname`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command results in a change that is only persistent until the next system boot.
{% endhint %}

The `/etc/hosts` file is used for mapping hostnames with IP addresses. It is a flat-fole with one record on each line. The format of the file is:

```bash
IP Address    Host Name    Alias
127.0.0.1      localhost
192.168.4.8    apps.sample.com      apps
192.168.4.12   vm1.sample.com       vm1
```

The `Alias` filed is used for mapping short names or labels to a host.

The functionality of the `/etc/hosts` file has been relegated by DNS but is still used in the following situations:

* **Bootstrapping**: This file is **referred to during system startup** since the DNS service is not started at this point.
* **Isolated nodes:** If a **node is not connected to the internet**, it is unlikely to use DNS. The `/etc/hosts` file is useful for such nodes.
* **NIS**: The records in the hosts file are **used as input for the NIS** (Network Information Services) database.

**`Systemd` systems use an alternative to the **<mark style="color:red;">**`hostname`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command, the **<mark style="color:red;">**`hostnamectl`**</mark><mark style="color:red;">**  **</mark><mark style="color:red;">****</mark>**  command**. Similar to the <mark style="color:red;">`hostname`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, the <mark style="color:red;">`hostnamectl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can also be used to query and set system hostnames, bu the `hostnamectl` provides additional categories for hostnames: _static_, _pretty_, and _transient_ which are described below:

* **Static**: A static hostname is limited to \[a-z], \[0-9], hyphen `-` , and period `.` characters (no spaces). This hostname is stored in the `/etc/hostname` file. Can be set by a user.
* **Pretty**: Hostname can be in human-readable format **using any valid UTF-8 characters** and can include special characters.
* **Transient**: Is a dynamic hostname usually **set by the kernel to `localhost` by default**. A dynamic hostname can be modified if needed. The transient hostname can be modified by DHCP or mDNS at runtime.

To demonstrate, in order to **view the current hostname**, simply use the <mark style="color:red;">`hostnamectl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> commnad by itself or with the <mark style="color:red;">**`status`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> subcommand:

```bash
sysadmin@localhost:~$ hostnamectl status 
      Static hostname: localhost
            Icon name: computer-vm
              Chassis: vm
           Machine ID: 5b91eb5e50594030b48b28f103cf5cd6
              Boot ID: c04fc73d939a4fc18b279cd46d08f8d7
       Virtualization: kvm
     Operating System: Ubuntu 18.04.2 LTS
               Kernel: Linux 4.15.0-45-generic
         Architecture: x86-64
```

To **change the local machine hostname**, use the <mark style="color:red;">`hostnamectl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command with the <mark style="color:red;">**`set-hostname`**</mark>** option**, this must be done with elevated permissions:

```bash
sysadmin@localhost:~$ sudo hostnamectl set-hostname netlab01
[sudo] password for sysadmin:
sysadmin@localhost:~$ hostnamectl status
      Static hostname: netlab01 
            Icon name: computer-vm
              Chassis: vm
           Machine ID: 5b91eb5e50594030b48b28f103cf5cd6
              Boot ID: c04fc73d939a4fc18b279cd46d08f8d7
       Virtualization: kvm
     Operating System: Ubuntu 18.04.2 LTS
               Kernel: Linux 4.15.0-45-generic
         Architecture: x86-64
```

### Configuring DNS

The DNS (Domain Name System) is the mapping table for the internet, allowing any computer or device to access websites, mail servrs, etc. by using a name instead of an IP address.

The `/etc/resolv.conf` file is the configuration for DNS resolvers. The information in this file is normally set up by network initialization scripts.

A sample `/etc/resolv.conf` file looks like the following:

```bash
# /etc/resolv.conf           
domain        sample.com
search        sample.com           
# central nameserver
nameserver    191.74.10.12
 
sortlist 191.74.10.0 191.74.40.0
```

The format of the `/etc/resolv.conf` is:

```
directive    value1, value2…
```

The configuration directives used in this file are:

| Option       | Meaning                                                                                                                                                                        |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `nameserver` | IP address of the name server that the resolver will use. Maximum of 3 servers can be listed                                                                                   |
| `domain`     | Domain name to be used locally                                                                                                                                                 |
| `search`     | Search list to be used for hostname lookup                                                                                                                                     |
| `sortlist`   | <p>Allow addresses to be sorted</p><p>The list is specified by IP addresses and optionally the netmask</p>                                                                     |
| `options`    | <p>Used to modify the resolver’s internal variables using certain keywords</p><p>E.g. <code>attempts: 3</code> will set the retry count for querying the name servers to 3</p> |

### Name Service Switch

The name service switch (NSS) is used by the system administrator to **specify which name information source** (i.e, local files, LDAP) **to use for different categories** (i.e, `passwd`, `shadow`, `hosts`,etc), and in which order the sources are searched.

The client applications query the name service database using APIs such as:

* `gethostbyname()`
* `getaddrinfo()`
* `getnetent()`&#x20;

The <mark style="color:orange;">`/etc/nsswitch.conf`</mark> file is **used to store the information used for name service switching.** It is a text file with columns that contain the following information:

* Database name
* Lookup order of sources
* Actions permitted for the lookup result

A process that needs to lookup host or network-related information will refer to the configuration for the required database in this file.

A sample portion of an <mark style="color:orange;">`/etc/nsswitch.conf`</mark> file will look like the following:

```bash
sysadmin@localhost:~$ cat /etc/nsswitch.conf                                    
...                                                            
passwd:         compat                                                          
group:          compat                                                          
⁠⁠ 
...
hosts:          files dns                                                       
networks:       files                                                           
...
```

The first column **contains the database name, followed by the services to be queried in the order of their occurences** in the file.

For example, the current sample of the <mark style="color:orange;">`/etc/nsswitch.conf`</mark> file demonstrates the `hosts` database service configured like the following:

```
host     files dns
```

When a hostname lookup is performed, the `files` entry will make use of the `/etc/hosts` file to perform the resolving. If the query does not return any results, then the query will be sent to the DNS resolver (`/etc/resolv.conf` )

By changing the order of the name services listed for a particular database, like `hosts` ,the administrator could change whether the local `/etc/hosts` file is consulted before or after the DNS servers listed in `/etc/resolv.conf`&#x20;

```
hosts:        dns files
```

In the event of the query not returning any results, specific actions can also be mentioned in the `/etc/nsswitch.conf` file.

```bash
hosts:        dns [NOTFOUND=return] files
```

In the above example, the DNS resolver will try to resolve the hostname. If a match is not found, then the resolver will immediately return `NOTFOUND` status and the `/etc/hosts` file will  not be queried.

### Configuring Routing Tables











