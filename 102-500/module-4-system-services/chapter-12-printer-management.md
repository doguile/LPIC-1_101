---
description: '108.4 : Manage printers and printing v2  Weight: 2'
---

# Chapter 12: Printer Management

<details>

<summary>Key terms</summary>

`/etc/cups/` Directory containing the configuratin files and directories for CUPS

`CUPS configuration files, tools and utilities` CUPS (Common UNIX Printing System) is an open source printing system developed by Apple and is the standard for Linux. CUPS includes a set of configuration files and directories located in `/etc/cups/` , a dameon (`cupsd`) used to **schedule print jobs**, filters for printing different document types, and commands to print and manage print jobs in the queue.

`lpd legacy interface (lpr, lprm, lpq)` The BSD LPD (Line Printer Daemon) is the standard printing system on several UNIX flavors. It provides printer services for both local and remote users. Basic command lines include: <mark style="color:red;">`lpr`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> (send jobs to the printer) , <mark style="color:red;">`lprm`</mark>( removes a print job from the print queue), and <mark style="color:red;">`lpq`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> (displays print jobs queued for printing).

</details>

## Introduction

From the system adminsitrator's perspective, printing is an essential service to understand and to know how to configure. This chapter will explore two common Linux printing systems, the **Common UNIX printing system (CUPS)** and the **LPD Printing system.**

A printer is most often used as a shared resource on the network. **The printing service is a central service that accepts print requests from multiple users**, queues each print job for the actual printing, tracks the status of every print job, and notifies users of printer and print job status.

## Undertstanding CUPS

CUPS is an opern source printing system developed by Apple, and available for many operating systems such as windows, Linux,and OS X. CUPS is the standard for printing services on Linux.

CUPS implements the **Internet Printing Protocol (IPP)** to send jobs from clients to print servers; **IPP is a network protocol for managing remote printing**. Supports authentication and access control.

When a document or web page is printed, CUPS creates a queue to keep track of the print job. The printer can be connected directly to the computer, or it could be on the network.The queue contains the name of the document being printed and can be monitored by the user. The user can cancel the job while it is in the queue. Once the job is completed, CUPS  removes it from the queue and sends the next job in the queue to the printer.

## Configuring CUPS

The <mark style="color:orange;">**`/etc/cups/`**</mark> directory **contains the configuration files for CUPS**. The key configuration files for CUPS are as follows:

| File Name                                       | Description                                                             |
| ----------------------------------------------- | ----------------------------------------------------------------------- |
| <mark style="color:red;">`cupsd.conf`</mark>    | Server configuration file                                               |
| <mark style="color:red;">`printers.conf`</mark> | Configuration file for individual printers                              |
| <mark style="color:red;">`classes.conf`</mark>  | Configuration file for printer classes (groups of printers)             |
| <mark style="color:red;">`snmp.conf`</mark>     | Configuration file to regulate remote browse access                     |
| <mark style="color:red;">`ppd/`</mark>          | Directory for printer drivers for the printers configured on the server |
| <mark style="color:red;">`ssl/`</mark>          | Directory for SSL encryption keys for remote access                     |

The <mark style="color:orange;">**`/etc/cups/cupsd.conf`**</mark> file is **used for configuring the CUPS server**. Some of the commonly used directives in this file are as follows:

| Directive         | Meaning                                                                                                                                                                                                                                   |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Allow`           | Allow access from the specified hostnames/addresses                                                                                                                                                                                       |
| `Listen`          | Listen to the specified hostname/address                                                                                                                                                                                                  |
| `AccessLog`       | Access log file name                                                                                                                                                                                                                      |
| `AuthType`        | Authentication type; valid values are: `None` (default value), `Basic` or `Digest`                                                                                                                                                        |
| `DataDir`         | Directory for the data files                                                                                                                                                                                                              |
| `DefaultCharSet`  | Default charset for text                                                                                                                                                                                                                  |
| `DefaultLanguage` | Default language to be used for web and text content (e.g., If the default language is English, then a web page with Japanese characters though displayed correctly in the web browser, may have junk characters in the printed version.) |
| `Deny`            | Deny access to the specified hostnames/addresses                                                                                                                                                                                          |
| `MaxCopies`       | Maximum number of copies that a user can print per job (default is 9999)                                                                                                                                                                  |
| `Browsing`        | Enables or disables browsing for locating remote printers (enabled by default)                                                                                                                                                            |
| `BrowseOrder`     | Specify the order of access control (`Deny,Allow` or `Allow,Deny`)                                                                                                                                                                        |
| `BrowseAllow`     | Allow incoming printer information packets from the specified hostnames/addresses                                                                                                                                                         |
| `BrowsePort`      | Port to listen to for printer information packets                                                                                                                                                                                         |

A default configuration file is provided with the CUPS package. By default, this configuration file will allow local access to its printers and show printers shared on the network.

A typical <mark style="color:orange;">**`/etc/cups/cupsd.conf`**</mark> file will contain directives similar to the following:

```bash
SystemGroup admin
 
# Default authentication type
DefaultAuthType Basic
 
# Restrict access to the server...
<Location />
  Order deny,allow
</Location>
 
# Restrict access to configuration files...
<Location /admin/conf>
  AuthType None
#  Require user @SYSTEM
  Order deny,allow
</Location>
 
# Listen for connections from the local machine.
Listen localhost:631
Listen /var/run/cups/cups.sock
 
# Show shared printers on the local network.
Browsing On
BrowseOrder allow,deny
BrowseAllow all
 
# Log general information in error_log
LogLevel info
 
# MaxClients: controls the maximum number of simultaneous clients that
# will be handled. Defaults to 100.
MaxClients 100
 
DataDir /usr/share/cups
 
DefaultCharset utf-8
 
DefaultLanguage en
 
# Set the default printer/job policies...
<Policy default>
  # Job-related operations must be done by the owner or an administrator...
  <Limit Send-Document Send-URI Hold-Job Release-Job Restart-Job Purge-Jobs Set-Job-Attributes Create-Job-Subscription Renew-Subscription Cancel-Subscription Get-Notifications Reprocess-Job Cancel-Current-Job Suspend-Current-Job Resume-Job CUPS-Move-Job>
#    Require user @OWNER @SYSTEM
    Order deny,allow
  </Limit>
 
 
  # Only the owner or an administrator can cancel or authenticate a job...
  <Limit Cancel-Job CUPS-Authenticate-Job>
#    Require user @OWNER @SYSTEM
    Order deny,allow
  </Limit>
 
  <Limit All>
    Order deny,allow
  </Limit>
</Policy>
```

{% hint style="info" %}
**Consider this**

The HTTP access control used in the CUPS configuration is adapted from Apache Server. The `Allow` and `Deny` keywords make it possible to allow, as well as deny, access by specifying the hostname/address. **The `Order` keyword ensures that conditional access can be given.**

For example, to allow access to all hosts in the `netdevgroup.com` domain while excluding those who are in the `uk.netdevgroup.com` subdomain, use the following directive:

```
Order Allow,Deny
Allow netdevgroup.com
Deny uk.netdevgroup.com
```
{% endhint %}

The <mark style="color:orange;">**`/etc/cups/printers.conf`**</mark> file is used by the `cupsd` daemon to **store the list of available local printers.** This is a plain text file, which can be updated using the web interface or the `lpadmin` command.

A sample `printers.conf` file would look like the following:

```bash
<DefaultPrinter Office-ES800>
DeviceURI parallel:/dev/lp0
Location Epson Stylus 800
State Idle
Accepting Yes
JobSheets none none
QuotaPeriod 0
PageLimit 0
KLimit 0
</Printer>
```

The **`DefaultPrinter` ** directive indicates the printer as the default print server. The `DeviceURI` directive specifies the device file allocated to this printer.

The CUPS Web Interface is the easiest way to configure and manage local and network printers and can be accessed through any web browser using the URL: _//localhost:631_

{% hint style="danger" %}
The CUPS Web Interface must be enabled to access it from your device.
{% endhint %}

The login credential of root will be needed for authentication, and the CUPS daemon must be running to perform any administrative tasks.

The <mark style="color:orange;">`/etc/cups/classes.conf`</mark> file is used by the <mark style="color:red;">`cupd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon to **store the list of available local clasess**.

{% hint style="warning" %}
_**Print classes are a set of printers that have been assigned a single name**_, so when a print job is sent to a print class, it will be printed by the first printer available in that class
{% endhint %}

{% hint style="info" %}
By default, CUPS can only be administered by the root user. Users who are members of the group specified in the `SystemGroup` directive in the `/etc/cups/cupsd.conf` file can also administer CUPS.
{% endhint %}

**The commnad line alternative to the CUPS Web Interface** program for adding CUPS printers and classes **is the **<mark style="color:red;">**`lpadmin`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command**. For example, to **add a new local printer** execute the <mark style="color:red;">**`-p`**</mark> option:

```bash
lpadmin -p testprinter -E -v parallel:/dev/lp
```

This will add a new printer called `testprinter` on the parallel port. The <mark style="color:red;">`-E`</mark> option **enables the printer and accepts jobs.**

To make `testprinter` the **default printer, execute **<mark style="color:red;">**`-d`**</mark>** option** to the `lpadmin` command followed by the name of the new printer.

```
lpadmin -d testnewprinter
```

To **delete** `testprinter`, **execute the **<mark style="color:red;">**`-x`**</mark>** option** to the <mark style="color:red;">`lpadmin`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command:

```
lpadmin -x testnewprinter
```

This will delete the printer and pending jobs in its queue. If a job is currently being printed, the it will be aborted.

## CUPS Scheduler

The **scheduler for CUPS is **<mark style="color:red;">**`cupsd`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and by default, it **runs as a daemon**. It implements all the operations and attributes specified by IPP.

{% hint style="info" %}
The scheduler runs as a single-threaded server process and uses other options for printing, monitoring, and providing web interfaces.
{% endhint %}

The scheduler stores job files in the `/var/spool/cups/` directory. Every print job scheduled will have one file containing IPP message data and one or more data files.

Some of the key options of `cupsd` are:

| Directive         | Meaning                                                                               |
| ----------------- | ------------------------------------------------------------------------------------- |
| `-c config_file`  | Use the specified configuration file instead of the default (`/etc/cups/cupsd.conf` ) |
| `-f`              | Run as a foreground process                                                           |
| `-F`              | Runs as a foreground process but detach from the controlling terminal                 |
| `-t`              | Verify the syntax of the configuration file                                           |

## CUPS Printing Queues

**Print queues manage the scheduling of print jobs on a specific printer**. The queues **can be added in CUPS by either using the **<mark style="color:red;">**`lpadmin`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command or by using the CUPS Web Interface**. The queues can be of the following types:

* Locally - connected printer
* Networked IPP (CUPS) - Refers to the queue of another CUPS printer server on the network
* Networked UNIX LPD - Refers to the queue of an LPD server on the network
* Networked Windows (SMB) - Refers to the queue of a Windows-based print server on the network
* Networked Novell - Refers to the queue printer connected to the Novell Netware server on the network.
* Networked JetDirect -  Refers to the queue of a network-connected HP printer that prints data received on a TCP/IP port

To add a new printer queue using the <mark style="color:red;">`lpadmin`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, execute the following:

```
lpadmin -p news -h localhost -v /dev/npp0
```

The <mark style="color:red;">`cupsenable`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`cupsdisable`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utilities are **used to start and stop printers and classes**, respectively.&#x20;

The utilities **used to accept and reject print jobs** to the specified destination are <mark style="color:red;">`cupsaccept`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`cupsreject`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ,respectively

To start a printer and enable queueing to accept jobs for a printer named `sales_dept`, execute the following commands:

```bash
root@localhost:~ cupsenable sales_dept
root@localhost:~ cupsaccept sales_dept
```

To stop queuing new jobs on a printer named `news` ,execute the following:

```
root@localhost:~ cupsreject news
```

{% hint style="info" %}
If certain printer needs to be taken offline for some maintenance, then the <mark style="color:red;">`cupsreject`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility can be used to prevent any more jobs from being queued to this printer.

The current jobs in the queue would, however, be processed, so to prevent the current print jobs from being printed, use the <mark style="color:red;">`cupsdisable`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command.&#x20;
{% endhint %}

## Troubleshooting General Printing Problems

The first thing to do when an error occurs in the CUPS service is to review the log files in the `/var/log/cups` directory. The different log files that are created:

1. Access log - The `access_log` file **contains the list of HTTP resources accessed by the clients** or through any web browser. It uses a log format, which is identical to that used by web servers.
2. Page Log - The `page_log` file **contains the accounting data for print jobs**. This file show information such as the printer name, user name, job number, date and time, current page number, and the number of copies.
3. Error Log - The `error_log` file **contains error and warning messages from the scheduler.** The data captured in  this file depends on the setting of the `LogLevel` directive in the `cupsd.conf`&#x20;

The messages contained in these log files are useful when trying to ascertain the source of a printing problem. if the problem is with CUPS, then it may be fixed by correcting one of the configuiration files previously discussed.

Other common problems:

1. The printer may have run out of paper or become jammed
2. The printer may be disabled and needs to be enabled
3. The printer may be rejecting jobs and needs to allow them
4. The network may be down or not working correctly
5. The host sharing the printer may be down or having other issues
6. The `cupsd` daemon may not be running so the service needs to be started
7. The filesystem where the print jobs are queued may be out of space

## Understanding LPD

The BSD LPD (Line Printer Daemon) is the standard printing system on several UNIX flavors. **CUPS supports LPD as well as IPP**. The implementation is based on RFC 1179 (Line Printer Daemon Protocol). It provides printer services for both local and remote users.

The <mark style="color:red;">**`lpd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon**, which is typically **started at boot time**, handles the spooling of jobs. When a **new job is queued using the **<mark style="color:red;">**`lpr`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command**, it will check for an available printer and then send the data to the printer.

{% hint style="info" %}
The <mark style="color:red;">`lpd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon uses the <mark style="color:orange;">`/etc/printcap`</mark> file to **discover the list of available printers.**
{% endhint %}

The <mark style="color:red;">`lpr`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command (line printer) is **used to send print jobs to the printer.** If a file name is specified, then it will be sent to the printer; otherwise, the data from standard input will be sent to the printer.

For example, to print the `info.txt` file to the **default printer, execute the **<mark style="color:red;">**`lpr`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command** following by the file:

```bash
lpr info.txt
```

To send the `info.txt` file to a **specific printer** named `floor1`; execute the `lpr` command **with the **<mark style="color:red;">**`-P`**</mark>** option** followed by the name of the printer:

```bash
lpr -P floor1 info.txt
```

To **print 3 copies** of the `info.txt` file, execute the following command:

```bash
lpr -# 3 infor.txt
```

The line printer remove <mark style="color:red;">`lprm`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to delete queued print jobs.** To remove a job, either the user name or the job name could be specified. If the job name refers to a job currently being printed, then printing will be stopped and restarted after removing this print job.

For example to **remove the last job that was submitted**, execute the <mark style="color:red;">`lprm`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command

```
lprm
```

> This will delete the last job submitted by the user. A user is permitted to delete their jobs only.

To **remove all print jobs from all queues**, use the <mark style="color:red;">`-a`</mark> option

```bash
lprm -a all
```

The _line printer_ queue <mark style="color:red;">`lpq`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to view the printer's queue status.** To view the status of the default printer, execute the `lpq` command:

```bash
sysadmin@localhost:~$ lpq
  Printer: hpfloor1@gsource  (dest hp@gsource)
     Queue: 3 printable jobs
     Server: no server active
     Status: job 'rrao@h5+16102' removed at 13:05:10
     Rank   Owner/ID             Class Job Files       Size    Time
    1      rrao@h4+16979      A 16979 (stdin)    3    13:10:01
    2      rrao@h4+16981      A 16981 (stdin)    6    13:15:10
    3      rrao@h4+17001      A 17001 (stdin)    5    13:20:15
```

To **remove job `3` from the `q2` printer's** queue, use the <mark style="color:red;">`-P`</mark> option followed by the printers queue, and the number of the job.

```bash
lpr -P q2 3
```

To **view the status** of the `q2` printer, execute the <mark style="color:red;">`lpq`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command followed by the <mark style="color:red;">`-P`</mark> option

```bash
sysadmin@localhost:~$ lpq -P q2
q2 is ready
no entries
```

To **view the status of all printers**, execute the <mark style="color:red;">`lpq -a`</mark> command:

```
sysadmin@localhost:~$ lpq -a
```

The line printer control <mark style="color:red;">`lpc`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to manage the print queues,** including starting and stopping the printers and changing the order of jobs on the print queues.

The `lpd` commands, such as `lpr`, `lprm`, `lpq`, are suported in newer printing systems such as CUPS and allow users to send jobs to the printers and control the print queues using the command line.
