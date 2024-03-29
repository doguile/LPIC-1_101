---
description: '110.2: Setup host security v2  Weight: 3'
---

# Chapter 17: Host Security

<details>

<summary>Key terms</summary>



</details>

## Intro

It is the responsability of the system administrator to secure each host on the organization's network. This chapter will look closely at `xinetd` ,the super server that is designed to protect the network and reduce the number of continuously running servers.

## Understanding `xinetd`&#x20;

A linux system will have several services running at any point in time, many of which are network services. To avoid running these services the entire time, **a master process **_**listens**_** for incoming TCP connections and then starts the corresponding process**.

> In a nutshell, the master process acts as an intermediary between network services and ports

**This process is known as the internet super-server or the **<mark style="color:red;">**`inetd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon**. The <mark style="color:red;">`inetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon was the original super-server, it has now been **replaced by the extended internet super-server **<mark style="color:red;">**`xinetd`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> on many Linux distributions.

{% hint style="danger" %}
A system **can have either the **<mark style="color:red;">**`inetd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** or **<mark style="color:red;">**`xinetd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** service** running, but **not both.**
{% endhint %}

The <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> service is now used on all Linux systems. It provides all the services offered by <mark style="color:red;">`inetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> plus additional security features and access control according to different criteria such as system utilization, time of day, and client machines.

The services managed by <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> can be either _single-threaded_ or _multi-threaded_.

* A _single-threaded_ service managed by <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> doest not spawn new processes; it will process incoming requests in a sequential manner.
* A _multi-threaded_ service spawns a new process to handle each new incoming request and is able to service requests in parallel.

To `start`, `stop`, or `restart` the <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon, execute the following:

```
/etc/init.d/xinetd [start, stop, restart]
```

The <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon is **configured by the** <mark style="color:orange;">**`/etc/xinetd.conf`**</mark> **file** or by creating **separate files for each service in the **<mark style="color:orange;">**`/etc/xinetd.d`**</mark>** directory**. The key fields of the configuration file are as follows:

| Option           | Meaning                                                              |
| ---------------- | -------------------------------------------------------------------- |
| `service`        | Name of the service as specified in the `/etc/services` file         |
| `flags`          | Sets attributes for the connection                                   |
| `socket_type`    | Set the network socket type                                          |
| `wait`           | Specifies whether the service is single-threaded or multi-threaded   |
| `user`           | Specifies the user                                                   |
| `group`          | Specifies the group                                                  |
| `server`         | Absolute path of the server program                                  |
| `only_from`      | Hostname or IP address allowed access to the server                  |
| `no_access`      | Hostname or IP address not allowed access to the server              |
| `access_times`   | Specifies the time range to access a service in `HH:MM-HH:MM` format |
| `log_on_failure` | Specifies the logging parameters                                     |
| `disable`        | Specifies if the service is disabled or not                          |

For example, a snippet of the `/etc/xinetd.d/telnet` file as below:

```bash
service telnet 
{ 
disable = no
flags = REUSE 
socket_type = stream 
user = root
wait = no 
server = /usr/sbin/in.telnetd 
only_from = 127.0.0.1 192.168.12.0/24
no_access = 192.168.12.11
log_on_failure += USERID 
}
```

To break down the contents of this example file:

| Line                                                                    | Meaning                                                                                                                                                                                                                                                          |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `disable = no`                                                          | Indicates that the `telnet` service is enabled. The double negative of `disable = no` means the service is, in fact, enabled.                                                                                                                                    |
| `flags = REUSE`                                                         | Indicates that a port can be reused by `telnet`.                                                                                                                                                                                                                 |
| `socket_type = stream`                                                  | Indicates a TCP connection.                                                                                                                                                                                                                                      |
| `user = root`                                                           | Indicates that the root user will be starting the `telnet` processes.                                                                                                                                                                                            |
| `wait = no`                                                             | Indicates that it is a multi-threaded process.                                                                                                                                                                                                                   |
| `server = /usr/sbin/in.telnet.d`                                        | Indicates the absolute path of the `telnet` executable file.                                                                                                                                                                                                     |
| `only_from = 127.0.0.1 192.168.12.0/24` and `no_access = 192.168.12.11` | Indicates that all hosts on the `192.168.12.0/24` subnet except `192.168.12.11` will be allowed `telnet` access. Since `192.168.12.11` is included in the subnet, it appears in both parameters. To resolve the conflict, the more exact match takes precedence. |
| `log_on_failure += USERID`                                              | Indicates that the `USERID` parameter should be used while logging along with the standard parameters defined in the `/etc/xinetd.conf` file.                                                                                                                    |

**If the file is changed, then the **<mark style="color:red;">**`xinetd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon should be restarted** by an administrator by executing the following command:

```bash
root@localhost:~ /etc/init.d/xinetd restart
```

The key options for the <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon are as follows:

| <mark style="color:red;">`-f config_file`</mark>    | Use the specified configuration file rather than the default file `/etc/xinetd.conf`     |
| --------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| <mark style="color:red;">`-dontfork`</mark>         | Used when `xinetd` should be created as a foreground process                             |
| <mark style="color:red;">`-limit proc_limit`</mark> | Sets the threshold for the maximum number of processes that can be initiated by `xinetd` |
| <mark style="color:red;">`-filelog log_file`</mark> | Append log messages to the specified file                                                |

An important aspect of the <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> is that it **can be used to active sockets** so that they are ready to receive requests from network services. The sockets are created by the <mark style="color:red;">`xinetd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon when needed or on-demand, once it receives a request for access.

> Puedes averiguar si tu sistema tiene instalado <mark style="color:red;">`xinetd`</mark>:
>
> ```bash
> root@localhost:~ dpkg -l xinetd
> root@localhost:~ aptitude search xinetd
> root@localhost:~ apt search xinetd
> ```

### systemd.socket units

For systems running <mark style="color:red;">`systemd`</mark>, **there is a systemd unit type that can be used to specify the details of an Inter-process Communication (IPC)**, network socket, or file system based FIFO. These `.socket` systemd unit allow a socket to be activated.

> The systemd socket units are associated with a systemd service; however, not all systemd services need socket activation

Below is an example systemd socket unit file:

```bash
[Unit]
Description=OpenBSD Secure Shell server socket
Before=ssh.service
Conflicts=ssh.service
ConditionPathExists=!/etc/ssh/sshd_not_to_be_run
 
[Socket]
ListenStream=22
Accept=yes
 
[Install]
WantedBy=sockets.target
```

The following table breaks down the fields and settings in the example `.socket` systemd unit file above:

| Option                | Meaning                                                                                                                      |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `Description`         | Description of the systemd socket unit.                                                                                      |
| `Before`              | The unit needs to start before the specified unit.                                                                           |
| `Conflicts`           | The unit will conflict with the specified unit if it tries to start while the specified unit is running.                     |
| `ConditionPathExists` | Unit will not run if the specified path does not exist. Can add `!` to the path to have unit not run if the path does exist. |
| `ListenStream`        | Specifies what network port the systemd unit should listen on.                                                               |
| `Accept`              | Specifies if the socket should accept network connections or not.                                                            |
| `WantedBy`            | Specifies any systemd components that are needed to properly operate.                                                        |
| `MaxConnections`      | Specifies the maximum number of connections to the systemd service unit.                                                     |

The <mark style="color:red;">`systemctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can be used with the <mark style="color:red;">`list-sockets`</mark> command to display the `systemd.socket` units on a system:

```bash
sysadmin@localhost:~$ systemctl list-sockets --all
LISTEN			     UNIT		         ACTIVATES
/dev/rfkill		        systemd-rfkill.socket	 systemd-rfkill.service
/run/dmeventd-client	        dmevent.socket		 dm-event.service
/run/dmeventd		        dm-event.socket		 dm-event.service
/run/lvm/lvmetad.socket	        lvm2-lvmetad.socket	 lvm2-lvmetad.service
/run/lvm.lvmpolld.socket        lvm2-lvmpolld.socket	 lvm2-lvmpolld.service
/run/systemd/fsck.progress      systemd-fsckd.socket	 systemd-fsckd.service
/run/systemd/initctl/fifo       systemd-initctl.socket	 systemd-initctl.service
/run/systemd/journal/dev-log    systemd-journald-dev-log.socket	systemd-journald.service
/run/systemd/journal/socket     systemd-journald.socket	 systemd-journald.service
/run/systemd/journal/stdout     systemd-journald.socket	 systemd-journald.service
/run/systemd/journal/syslog     syslog.socket		 ryslog.service
/run/udev/control	        systemd-udev-control.socket	systemd-udev.service
/run/uuid/request	        uuidd.socket		  uuid.service
```

To display the `systemd.socket` unit configuration files, use the <mark style="color:red;">`systemctl list-unit-files`</mark> command with the <mark style="color:red;">`--type=socket`</mark> option:

```bash
systemctl list-unit-files --type=socket
UNIT FILE                           STATE
dbus.socket                         static
dm-event.socket                     enabled
lvm2-lvmetad.socket	           enabled
lvm2-lvmpolld.socket                enabled
ssh.socket                          disabled
syslog.socket                       static
systemd-fsckd.socket                static
systemd-initctl.socket            static
```

## Configuring TCP Wrappers

TCP wrappers are host-based access control systems that **extend the abilities of **<mark style="color:red;">**`xinetd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** to provide an additional layer of security** by defining which hosts can access _wrapped_ network services.

> A _wrapper_ is a network service which is accessed via a proxy or front end service. TCP Wrappers should be used in conjuction with a firewall and other security enhancements.

Some of the common Linux applications that are compiled with `tcp_wrappers` include <mark style="color:red;">`xinetd`</mark>, <mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`ssd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> (Secure Shell daemon)

The `tcp_wrappers` package is used to **provide access control to network services**. For a regular network service to use TCP wrappers, it **must be compiled using the **<mark style="color:orange;">**`/usr/lib/libwrap.a`**</mark> library.

> One method used to determine if a service is a TCP wrapped service is to use the <mark style="color:red;">**`strings`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command to display the plain text of a binary executable and **search for the term `hosts_access` .**
>
> If the term **`hosts_access`** exists, the service is TCP wrapped.

In the example below, `sshd` is a TCP wrapped service and `in.telnetd` (`telnetl` daemon) is not:

```bash
sysadmin@localhost:~$ strings /usr/sbin/sshd | grep hosts_access
hosts_access
sysadmin@localhost:~$ strings /usr/sbin/in.telnetd | grep hosts_access
```

```bash
sysadmin@localhost:~$ strings -f /usr/sbin/* | grep hosts_access
```

> <mark style="color:red;">`-f`</mark> especifica el fichero donde se encuentra la linea especificada por la tuberia

A second method to verify if a program is compiled with `tcp_wrappers` is to execute the _list dynamic dependencies_ <mark style="color:red;">`ldd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command:

```bash
sysadmin@localhost:~$ ldd program_name | grep libwrap
```

If the output of the <mark style="color:red;">`ldd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command contains **`libwrap`**, then the service supports TCP wrappers. The example below illustrates that <mark style="color:red;">`sshd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> is TCP wrapped and <mark style="color:red;">`in.telnetd`</mark> is not.

```bash
sysadmin@localhost:~$ ldd /usr/sbin/sshd | grep libwrap                             
        libwrap.so.0 => /lib/x86_64-linux-gnu/libwrap.so.0 (0x00007f782690b000) 
sysadmin@localhost:~$ ldd /usr/sbin/in.telnetd | grep libwrap
```

The <mark style="color:red;">`tcp_wrapper`</mark> library uses two files, the <mark style="color:orange;">`/etc/hosts.allow`</mark> and <mark style="color:orange;">`/etc/hosts.deny`</mark> files, to **control access**. These files contain rules that match service and hosts to **either grant or deny access to the specified service**.

**The **<mark style="color:orange;">**`hosts.allow`**</mark>** file has precedence over the **<mark style="color:orange;">**`hosts.deny`**</mark>** file** as the rules in the `hosts.allow` file are parsed first.

When a connection request is sent to a TCP wrapped service, `hosts.allow` and `hosts.deny` files are referenced to check if the host should be allowed to connect. If the connection is allowed, then `tcp_wrapper` simply hands over control to the service for further processing. Otherwise, if the connection is denied, then processing will halt.&#x20;

{% hint style="info" %}
The <mark style="color:red;">**`tcp_wrapper`**</mark>** ** daemon uses the <mark style="color:red;">**`syslogd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> daemon for logging information to <mark style="color:orange;">**`/var/log/messages`**</mark>&#x20;
{% endhint %}

{% hint style="danger" %}
**Note that if no rule matches from either file, access is granted**. That means in order to deny access to a service, you must make a rule in the `hosts.deny` file.
{% endhint %}

A typical rule will look like the last line in the example <mark style="color:orange;">`hosts.allow`</mark> file below

```bash
sysadmin@localhost:~$ more /etc/hosts.allow                                         
sendmail: all                                                                   
# /etc/hosts.allow: list of hosts that are allowed to access the system.        
#                   See the manual pages hosts_access(5) and hosts_options(5).  
#                                                                               
# Example:    ALL: LOCAL @some_netgroup                                         
#             ALL: .foobar.edu EXCEPT terminalserver.foobar.edu                 
#                                                                               
# If you're going to protect the portmapper use the name "rpcbind" for the      
# daemon name. See rpcbind(8) and rpc.mountd(8) for further information.        
#                                                                               
sshd: .netdevgroup.com
```

This last rule in the <mark style="color:orange;">**`hosts.allow`**</mark> file specifies that connection requests for the SSH daemon originating from the `netdevgroup.com` domain should be allowed.

**IP addresses and networks can also be specified in rules**. For example, to allow a specific host and a couple of networks access to the SSH daemon, add the following rule to <mark style="color:orange;">`/etc/hosts.allow`</mark>:

```bash
sshd:  192.168.1.10,192.168.0.0/24,192.168.2.0/255.255.255.0
```

If a system has multiple IP addresses, **the service specification can specify the address to which the service is bound following the `@` symbol.** The following rule will apply to any inbound connection on the `192.168.0.254` network interface:

```bash
sshd@192.168.0.254:  192.168.0.0/24
```

The `ALL` keyword is used to allow/deny access to all hosts. For example, consider the following entry in `hosts.deny`&#x20;

```bash
sshd,vsftpd: ALL
```

> This prevent `sshd` and `ftp` connection from any host

The `ALL` keyword may also be used to represent the services and hosts. For example, an excellent way to configure the `hosts.deny` file is to have the following entry as the last rule of the file:

```
ALL: ALL
```

With this rule, all connections would be blocked expect for those specifically permitted in the `hosts.allow` file.

The following table lists the additional keywords that may be used to specify the host:

| Keyword    | Meaning                                                                                                                    |
| ---------- | -------------------------------------------------------------------------------------------------------------------------- |
| `LOCAL`    | A host whose name does not contain a dot (period) character. These are hosts that are local to your network.               |
| `UNKNOWN`  | Matches any host whose name or address is unknown. Use carefully, as name resolution issues may cause names to be unknown. |
| `KNOWN`    | Matches any hostname and address which can be resolved.                                                                    |
| `PARANOID` | Matches any host whose name does not match its resolved address.                                                           |

{% hint style="info" %}
Since **TCP Wrappers are implemented as a separate library** with its own configuration files, they **can be used by multiple networks services** that require a common configuration.
{% endhint %}

## Denying Access to Users

The <mark style="color:orange;">**`/etc/nologin`**</mark> file is used to **prevent all users except root from logging** on to the system.

The **system administrator can create the file by using a text editor** or by executing a command similar to the following:

```bash
root@localhost:~ echo 'System down for maintenance until 2pm' > /etc/nologin   
root@localhost:~ cat /etc/nologin                                              
System down for maintenance until 2pm
```

If a user other than root tries to log in to the system, the contents of the <mark style="color:orange;">`/etc/nologin`</mark> file are displayed on the user's terminal and login is denied.

**All users should have read permissions to this file** because the <mark style="color:red;">`login`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command refers to this file if it exists.

The <mark style="color:orange;">**`/etc/nologin`**</mark>** file should be deleted** by the system administrator **once the system is ready to be accessed by all users.**

{% hint style="warning" %}
The <mark style="color:orange;">**`/etc/nologin`**</mark> file is **used to temporarily prevent all users from starting new sessions** on the system except root.

The <mark style="color:orange;">`/etc/passwd`</mark> and <mark style="color:orange;">`/etc/shadow`</mark> files are used to selectively control which users can access the system
{% endhint %}

## Understanding Init

The <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> process is the heart of the operating system; it is the first process started by the kernel; hence, it is **given the PID (Process ID) of 1**.

The <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> process reads the `/etc/inittab` file that defines the system's initialization process, including which services and programs start during bootup and the default _runlevel_ of the system.

> Runlevels are functional states of the operating system that define what features and services are available

The standard runlevels for Debian and Red Hat-derived systems vary slightly,as indicate below:

| Runlevel     | Debian-based Systems                                                                  | Red Hat-based Systems                                                                        |
| ------------ | ------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `0`          | Halt the system                                                                       | Halt the system                                                                              |
| `1` (or `S`) | Single-user text mode (typically used for maintenance - similar to Windows Safe Mode) | Single-user text mode (typically used for maintenance - similar to Windows Safe Mode)        |
| `2`          | <p>DEFAULT</p><p>Graphical multi-user mode plus networking</p>                        | Not used (user-definable)                                                                    |
| `3`          | Same as 2, but not used                                                               | <p>DEFAULT</p><p>Multi-user mode with a console (text-based) login, plus networking mode</p> |
| `4`          | Same as 2, but not used                                                               | Not used (user-definable)                                                                    |
| `5`          | Same as 2, but not used                                                               | Graphical multi-user mode (with an X-based login screen)                                     |
| `6`          | Reboot                                                                                | Reboot                                                                                       |

To determine the current system runlevel, use the `runlevel` command:

```bash
sysadmin@localhost:~$ runlevel
N 3
```

The first character is the previous runlevel. `N` indicates the system hasn’t switched runlevels since booting. The `3` indicates the current runlevel `.unknown` would indicate the system is unable to determine the system runlevel.

The default runlevel for the system is defined by the following line in the `/etc/inittab` file:

```
id:3:initdefault:
```

{% hint style="info" %}
On Debian-based systems, the `/etc/inittab` file may not exist as modern Debian-based system running `systemd` use targets rather than runlevels.

The target translates as a runlevel for compatibility. If a system has reached the `multi-user.target` , then that will be translated to runlevel `3`; if a system has reached the `graphical.target`, then that is trasnlated to runlevel `5`

```bash
root@localhost:~ systemctl get-default
graphical.target
```
{% endhint %}

The default runlevel should not be changed unless there is a compelling reason to do so. The system administrator should verify that the entries in the <mark style="color:orange;">`/etc/inittab`</mark> file are correct and remove any entries which are not required.

## Init scripts

The <mark style="color:orange;">**`/etc/init.d`**</mark> directory contains two types of scripts:

* Scripts which are called directly by the <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> process.
* Scripts which are called indirectly by the <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> process via the <mark style="color:red;">**`rc`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**(run control)**</mark> script, which is **used while switching runlevels**

The scrips that are specific to each runlevel are present int the <mark style="color:orange;">`/etc/init.d`</mark> directory. Symbolic links to these files are created in the `/etc/init.d/rc0.d - /etc/rinit.d/rc6.d` directories. Each of these scripts understands the following parameters:

| Option    | Meaning                                                                                             |
| --------- | --------------------------------------------------------------------------------------------------- |
| `Start`   | Start the service.                                                                                  |
| `Stop`    | Stop the service.                                                                                   |
| `Restart` | Stop and start the service again if it is running already. If the service is not running, start it. |
| `Status`  | Display the current status of the service.                                                          |
| `Reload`  | Reload the service’s configuration file without restarting the service.                             |

For example, to restart the networking service, execute the following command:

```bash
root@localhost:~ /etc/init.d/networking restart
```

{% hint style="info" %}
Generally, scripts for services such as networking, FTP, SSH, and Apache are kept in the <mark style="color:orange;">**`/etc/init.d`**</mark> directory
{% endhint %}

The programs can be linked with different runlevels by creating symbolic links in the corresponding folders. For example, the networking scripts are used at runlevel 3 and the symbolic links for these scripts are placed in the `/etc/init.d/rc3.d` directory.

An **administrator can switch from one runlevel to another using the **<mark style="color:red;">**`init`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command** without having to reboot the system. For example, to switch from runlevel 2 to 3, the process would be:

1. The root user initiates the runlevel switch by executing <mark style="color:red;">`init 3`</mark>
2. The init process refers to the `/etc/inittab` file
3. The `/etc/init.d/rc` scripts will be run with parameter `3`
4. The <mark style="color:red;">`rc`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> (run control) script will stop the appropiate services of the previous runlevel 2 and start the services of the new runlevel 3.
