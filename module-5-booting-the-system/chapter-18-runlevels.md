---
description: >-
  101.3: Change runlevels / boot targets and shutdown or reboot system v2 
  Weight: 3
---

# Chapter 18: Runlevels

<details>

<summary>Key Terms</summary>

**`/etc/init.d`** Contains scripts used by the System V init tools (SysVinit)

**`/etc/inittab`** Configuration file read by `init` when it starts up to determine what process to start for various run levels

**`/etc/systemd`** The location of various unit files that control services controlled by systemd

**`/usr/lib/systemd`** The location of services provided by installed packages

**`init`** The parent of all processes on the system, it is executed by the kernel and is responsible for starting all other processes.

**`shutdown`** Arranges for the system to be brought down in a safe way

**`systemctl`** The interface to systemd, the init system used by many Linux distributions. It combines the functionallity of both services and chkconfig into a single file tool that you can use for instance to enable/disable permanently or only for the current session.

**`systemd`** A full replacement for `init` with parallel starting of services and other features, used by many distros.

**`telinit`** Signal `init` to change the system's runlevel. `telinit` is actually just a link to `init`&#x20;

**`wall`** Displays a message, or the contents of a fille, or otherwise its stadin, on the terminals of all currently logged in users.

</details>

## Introduction

**Linux uses the concept of different **_**runlevels**_ to define what services or processes will be running. Although the Linux Kernel can recognize runlevel values from 0 to 9.

> Typically only runlevels 0 through 6 are used.

Traditionally, <mark style="color:red;">**`init`**</mark> and <mark style="color:red;">**`Upstart`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> used these _<mark style="color:green;">**runleves**</mark>_ to define which services were started according to the needs of a particular runlevel.

**These programs have been replaced on many distributions by** <mark style="color:red;">**`systemd`**</mark>. It does something similar to runlevels called _<mark style="color:green;">**targets**</mark>_, which are shown in the following table with their runlevel equivalent.

| Runlevel | Purpose                                                                   | systemd Target      |
| -------- | ------------------------------------------------------------------------- | ------------------- |
| 0        | Halt or shut off the system                                               | `poweroff.target`   |
| 1        | Single-user mode for administrative tasks                                 | `rescue.target`     |
| 2        | Multi-user mode without configured network interfaces or network services | `multi-user.target` |
| 3        | Normal startup of the system                                              | `multi-user.target` |
| 4        | User-definable                                                            | `multi-user.target` |
| 5        | Start the system normally with a graphical display manager                | `graphical.target`  |
| 6        | Restart the system                                                        | `reboot.target`     |

## Default Runlevel

Systems using traditional <mark style="color:red;">**`init`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> can specify the default runlevel by modifying the **`/etc/inittab`** file entry that looks like the following:

```bash
id:5:initdefault:
```

In this example, the default **`runlevel`** indicated is for the system to go to `runlevel 5`, which is typical for a desktop or laptop system that will be runnning a GUI, and will, most likely, be used by an end user.

> For most Linux systems, runlevel 5 provides the highest level of functionality, including providing a GUI interface.

Servers typically don't offer a GUI interface, so the `initdefault` entry might look like:

```
id:3:initdefault:
```

**If the system is using** <mark style="color:red;">**`Upstart`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> instead of the traditional **`init`** process, then the default `runlevel` may also be set in the <mark style="color:red;">**`/etc/inittab`**</mark> file, as is the case with distributions derived **from Red Hat Enterprise Linux 6**.&#x20;

On the other hand, distributions like **Ubuntu** can be changed by setting the `DEFAULT_RUNLEVEL` environmental variable in the <mark style="color:red;">**`/etc/init/rc-sysinit.conf`**</mark> file.

{% hint style="danger" %}
**systemd** doesn't natively use runlevels, but it has something similar called _**targets**._
{% endhint %}

To set a default target in **`systemd`**, create a symbolic link from the target definition found in the <mark style="color:red;">`/lib/systemd/system/`</mark> directory to the <mark style="color:red;">`/etc/systemd/system/default.target`</mark> file. This file is a symbolic link that controls where the system first boots into.

```bash
root@ubuntu:/lib/systemd/system: ln -sf graphical.target /etc/systemd/system/default.target
root@localhost:~# ls -l /etc/systemd/system/default.target                      
lrwxrwxrwx 1 root root 36 Apr  8 08:24 /etc/systemd/system/default.target -> /li
b/systemd/system/graphical.target
```

## Viewing current runlevel

One of the commands that displays the current runlevel is the <mark style="color:red;">**`runlevel`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command, which **shows the previous runlevel first, followed by the current runlevel**. If no previous runlevel was achieved, then it will show **N** for the previous runlevel.

```bash
root@ubuntu:~# runlevel
N 2
```

The <mark style="color:red;">**`who -r`**</mark> command also **displays the current system runlevel**. One benefit of this technique is that it will display the date and time that the current runlevel was reached:

```bash
root@ubuntu:~# who -r
        run-level 2  2019-05-29 14:25
```

## Changing Runlevels and Targets

Both the traditional _**SysVinit**_** ** and _**Upstart**_** ** support **passing runlevels to the kernel as parameters** from the bootloader to override the default runlevel.

To specify a different runlevel at boot time on a system that uses **systemd**, append to the kernel parameters an option with the following syntax where `DESIRED.TARGET` is one of the **systemd** targets.

```bash
systemd.unit=DESIRED.TARGET
```

**The root user can also change runlevels while the operating system is running by using several commands**, including the <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`telinit`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> commands, which allow the desired runlevel to be specified.

## The <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`telinit`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Commands

To directly specify the runlevel to go to, either use <mark style="color:red;">**`init`**</mark>** ** or <mark style="color:red;">**`telinit`**</mark>. The <mark style="color:red;">**`telinit`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command in some distro has a <mark style="color:red;">`-t`</mark> option, which **allows for a time delay in seconds to be specified**; otherwise the <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`telinit`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command are functionally identical.

{% hint style="info" %}
In fact, on some systems, the <mark style="color:red;">`telinit`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command may be a simple link to the <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command`.`
{% endhint %}

To use these commands, simply specify the desired runlevel as an argument. For example, to reboot the system, use either the `init 6` command or the `telinit 6` command.&#x20;

```bash
root@localhost:~ init 6
root@localhost:~ telinit 6
```

{% hint style="danger" %}
Changing runlevels will affect the applications or services you are running and can cause data loos or connection interruption for users accessing the system for those services.
{% endhint %}

With the **systemd** replacement for <mark style="color:red;">`init`</mark>, the <mark style="color:red;">`init`</mark> command can still be used to modify the `runlevel`; **systemd will translate the desired runlevel to a **<mark style="color:red;">**target**</mark>**.**  For example, if `init 5` is executed, then systemd would attempt to change to the `graphical.target` state.

To have **`systemd` ** natively switch to a target state, with root privileges execute:

```bash
systemctl isolate DESIRED.TARGET
systemctl isolate rescue.target
```

### The halt, poweroff, reboot, and shutdown Commands

To bring the system down to runlevel zero, execute the <mark style="color:red;">`halt`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ,<mark style="color:red;">`poweroff`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> or <mark style="color:red;">`shutdown`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command.&#x20;

While the <mark style="color:red;">`halt`</mark> and <mark style="color:red;">`poweroff`</mark> commands will begin shutting down the system immediately, **the **<mark style="color:red;">**`shutdown`**</mark>** command requires a time argument to indicate when the shutdown should begin**.&#x20;

> Formats of this time argument can be the word **`now`**, a countdown time in the `HH:MM` format, or the number of minutes to delay in the `+M` format

A message will appear in the terminals of all users can also be specified with the <mark style="color:red;">`shutdown`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command.

```
root@localhost:~# shutdown now "System going down for repairs"
```

Oddly enough, **if an option is not specified**, then the <mark style="color:red;">**`shutdown`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command will actually take the system down **to **_**runlevel**_** 1**.&#x20;

The <mark style="color:red;">**`shutdown`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command used with the <mark style="color:red;">**`-r`**</mark> option is similar to using the <mark style="color:red;">**`reboot`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command and will cause the system **to go to **_**runlevel**_** 6.**&#x20;

The <mark style="color:red;">**`shutdown`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command used with the <mark style="color:red;">**`-h`**</mark> option is similar to using the <mark style="color:red;">**`halt`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command and will cause the system **to go to **_**runlevel**_** 0.**

## The `wall` Command

The <mark style="color:red;">**`wall`**</mark>** ** command can be **used to display messages or the contents of a file to all users on the system**.&#x20;

```bash
sysadmin@localhost:~$ echo -e "The server will be offline on Saturday
from\n6:00PM to 12:00PM for scheduled maintenance" | wall
Broadcast message from sysadmin@localhost (console) (Wed May 29 22:13:59
2019):

The server will be offline on Saturday from
```

The <mark style="color:red;">**`wall`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command accepts standard input or the name of a file. To display a file, the <mark style="color:red;">**`wall`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command either **requires the user to have root privileges or the contents to be piped from another command**, such as <mark style="color:red;">`cat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command.&#x20;

```bash
sysadmin@localhost:~/Documents$ wall letters.txt
wall: will not read letters.txt - use stdin.
sysadmin@localhost:~/Documents$ sudo wall letters.txt
[sudo] password for sysadmin:

Broadcast message from sysadmin@localhost (console) (Wed May 29 22:15:23
2019):

a
b
c
d
e
sysadmin@localhost:~/Documents$ cat letters.txt | wall

Broadcast message from sysadmin@localhost (console) (Wed May 29 22:17:44
2019):

HOLA 
```

The <mark style="color:red;">`-n`</mark> option can be used by the <mark style="color:red;">`wall`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command to **suppress the leading banner. (only available for root)**

## Managing system services

Typically, administrators will want to automate the management of services, so when the system is taken to a specific runlevel or taget state, they will know what services should automatically be available.

If a system is using traditional <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> process to manage system services, then the scripts in the **`/etc/rc.d/init`** directory are used to manage the state of those services. For convenience, this directory, will usually have a symbolic link from the `/etc/init.d` file. The scripts in this directory are often referred to as <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> scripts.

To manually manage the state of a service, such as a web server, use the appropiate script in the **`/etc/rc.d/init.d/`** directory to start, stop, or otherwise chenge the state of the web server.

> For example, on a Red Hat Enterprise Linux distribution, the script to manage the web server has a path name of /etc/rc.d/init.d/httpd. So, to manually start the web server, you will execute the following command as the root user:
>
> ```bash
> [root@localhost ~]: /etc/rc.d/init.d/httpd start
> Starting httpd:
> ```

I**nstead of having to type the full path name to the script**, many systems provide a <mark style="color:red;">**`service`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> script that **allows the **<mark style="color:red;">**`init`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** script to be executed** without having to type the full path name to the script.&#x20;

```bash
[root@localhost ~]: service httpd start
[root@localhost ~]: service httpd stop
```

Different scritps have different capabilities or functions that they can perform. To discover what a script can do, execute the script without any argument.

```python
[root@localhost ~]# /etc/init.d/httpd
Usage: httpd {start|stop|restart|conderestart|try-restart|force-reload|reload|status|fullst
atus|graceful|help|configtest}
```

|    Argument   | Function                                                                                                                                                                                                                            |
| :-----------: | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|    `start`    | If the service is not running, then attempt to start it.                                                                                                                                                                            |
|     `stop`    | If the service is running, then attempt to stop it.                                                                                                                                                                                 |
|   `restart`   | Stop and then start the service over. If a major configuration change is made to a service, it may have to be restarted to make the change effective.                                                                               |
| `condrestart` | Restart the service on the condition that it is currently running.                                                                                                                                                                  |
| `try-restart` | Same as `condrestart`.                                                                                                                                                                                                              |
|    `reload`   | Read and load the configuration for the service. Reloading the configuration file of a service is normally a less disruptive way to make configuration changes to a service effective, but may not be successful for major changes. |
|    `status`   | Show whether the service is stopped or the process id (PID) if the service is running. Note: It is also possible to use the `service --status-all` command to see the status of all daemons.                                        |
|  `fullstatus` | For the Apache web server, displays the URL `/server-status`.                                                                                                                                                                       |
|   `graceful`  | For the Apache web server, it gracefully restarts the server. If the server is not running, then it is started. Unlike a normal restart, open connections are not aborted.                                                          |
|     `help`    | Displays the usage of the script.                                                                                                                                                                                                   |
|  `configtest` | Checks the configuration files for correctness. For some services, if the configuration file is modified, then this can be used to verify that the changes have no syntax errors                                                    |

## Runlevel Directories

With traditional <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> process, **specific directories are used to manage which services will be automatically started or stopped at different `runlevels`. **<mark style="color:red;">****</mark> In many Linux distro, these directories all exist within the **`/etc`** directory **** and have the following path names:

* rc0.d
* rc1.d
* rc2.d
* rc3.d
* rc4.d
* rc5.d
* rc6.d

**The number in the directory name represents the runlevel that it manages**, for example, `rc0.d` is for runlevel 0 and `rc1.d` is for runlevel 1.&#x20;

```bash
sysadmin@localhost:/etc$ ls -d rc*
rc0.d  rc1.d  rc2.d  rc3.d  rc4.d  rc5.d  rc6.d  rcS.d
```

To have a service started in a runlevel, a symbolic link to the <mark style="color:red;">`init`</mark> script in the `/etc/rc.d/init.d` directory can be created in the appropiate runlevel directory.

{% hint style="info" %}
This link name must **start** with the letter <mark style="color:red;">`S`</mark> ,followed by a number from **1 to 99**, and the same of the <mark style="color:red;">`init`</mark> script that it is linked to.
{% endhint %}

```bash
sysadmin@localhost:/etc/rc2.d$ ls
S01bind9             S01cron  S01irqbalance  S01rsync    S01ssh
S01console-setup.sh  S01dbus  S01plymouth    S01rsyslog  S01uuidd
```

For example, when the web server is set to start on a Linux system in runlevel 5, there is a symbolic link in the `/etc/rc.d/rc5.d` directory name <mark style="color:red;">`S85httpd`</mark> that is linked to the `/etc/rc.d/init.d/httpd` script:

```bash
[root@localhost ~]# ls -l /etc/rc.d/rc5.d/S85httpd
lrwxrwxrwx 1 root root 19 Jun 27 16:53 /etc/rc.d/rc5.d/S85httpd ->
../init.d/httpd
```

To manually create this link, you would execute the following command:

```bash
[root@localhost ~]# ln -s /etc/rc.d/init.d/httpd /etc/rc.d/rc5.d/S85httpd
```

Just as the <mark style="color:red;">`S`</mark> file links in a runlevel directory will indicate that a service is supposed to be _**started**_, the <mark style="color:red;">`K`</mark> file links in a runlevel directory will indicate that a service is supposed to be _**stopped**_ (_killed_).

Using the web server as an example again; to have the web server stopped at runlevel 5, create a symbolic link in the `/etc/rc.d/rc5.d` directory that would start with the letter <mark style="color:red;">**`K`**</mark>, **followed by a number from 1 to 99,** and the name of the <mark style="color:red;">`init`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> script that it is linked to:

```
[root@localhost ~]# ls -l /etc/rc.d/rc5.d/K15httpd
lrwxrwxrwx 1 root root 19 Jun 27 16:53 /etc/rc.d/rc5.d/K15httpd -> ../init.d/httpd
```

The reason that both **start** and **stop** links have a number after the letter <mark style="color:red;">**`S`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> or <mark style="color:red;">**`K`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> is to ensure that **services are started or stopped in the correct sequence**. The scripts are started (or stopped) in order, so `K15httpd` would be executed before `K35vncserver`.

So, what number is supposed to be provided to a specific script for <mark style="color:red;">`S`</mark> and <mark style="color:red;">`K`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ? Look at the script itself for the line that contains <mark style="color:red;">`chkconfig`</mark>.

```bash
[root@localhost ~]: grep chkconfig /etc/init.d/httpd
# chkconfig: - 85 (S) 15(K)
```

The second to last number **`85` ** of the `chkconfig` line is the <mark style="color:red;">`S`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> number to place on this script, the last number **`15` ** is the <mark style="color:red;">`K`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> number.

## The `chkconfig` command

The <mark style="color:red;">**`chkconfig`**</mark> command can be used to **view what services will be started for different runlevels**. This command can also be used to **turn on or turn off a service for specific runlevels**.&#x20;

{% hint style="danger" %}
On **Linux** distro that are not Red Hat-derived, this tool **may not be available.**
{% endhint %}

To view all the services that are set to start or stop automatically, the administrator can execute the <mark style="color:red;">**`chkconfig --list`**</mark> command and the output would look like the following:

```bash
[root@localhost ~]# chkconfig --list
auditd          0:off   1:off   2:on    3:on    4:on    5:on    6:off
crond           0:off   1:off   2:on    3:on    4:on    5:on    6:off
httpd           0:off   1:off   2:off   3:off   4:off   5:off   6:off
iptables        0:off   1:off   2:on    3:on    4:on    5:on    6:off
netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
```

The output of each line shows the name of the script file found in the `/etc/rc.d/init.d` directory, followed by each runlevel number, a colon, and wether the service is set be on or off.

To view a single service's settings, use the <mark style="color:red;">**`chkconfig --list SCRIPT`**</mark> command where SCRIPT is the name of a script file found in the **`/etc/rc.d/init.d`** directory.&#x20;

```bash
[root@localhost ~]# chkconfig --list httpd
httpd            0:off   1:off   2:off    3:off    4:off    5:off    6:off
```

To enable the service to start for most runlevels, use the <mark style="color:red;">**`chkconfig SERVICE on`**</mark> command.

```bash
[root@localhost ~]# chkconfig httpd on
```

In the `/etc/rc.d/init.d` script, there is a line that contains the following:

```
[root@localhost ~]# chkconfig: - 85 15
```

The `-` indicates that the **service is not enabled in any runlevels automatically** when it is first added to `chkconfig` management. In other words, this service is not set to start automatically unless an administrator uses the `chkconfig httpd on` command.

Some scripts have a different <mark style="color:red;">**`chkconfig`**</mark> value; for example, the `etc/rc.d/init.d/atd` script has the following line:

```
[root@localhost ~]# chkconfig:   345 95 5
```

> The **`345`** means that `atd` defaults to **being enabled in runlevels 3, 4, and 5**. The second value (**95**) is the **start** number for the script, and the third value (**5**) is the **stop** value for the script.

To turn on or off services for a non-default level, the <mark style="color:red;">**`--level`**</mark> option can be used with the <mark style="color:red;">**`chkconfig`**</mark> command. For example, the following two commands would ensure that the **`atd`** service was available in runlevels 2 and 4, but not available in runlevels 3 and 5:&#x20;

```bash
[root@localhost ~]# chkconfig --level 24 atd on
[root@localhost ~]# chkconfig --level 35 atd off
```

Two other <mark style="color:red;">**`chkconfig`**</mark> options should also be mentioned, although they are rarely used directly. The `chkconfig` options <mark style="color:red;">**`--add`**</mark> and <mark style="color:red;">**`--del`**</mark> can be used manually, but normally they are automatically used when an administrator either installs a new service software package or removes it.

If an administrator were to create an **`init`** script named `serviced` and store it in the `/etc/rc.d/init.d` directory, the <mark style="color:red;">**`chkconfig --add SERVICE`**</mark> command would need to be executed first before using either the `chkconfig SERVICE on` or `chkconfig SERVICE off` command.

The <mark style="color:red;">**`chkconfig --del SERVICE`**</mark> command would remove any links in the runlevel directories and services would not be started or stopped automatically at any runlevel.

## The `/etc/init` Directory

For users of Debian-derived Linux distributions, the **`/etc/init`** directory is used to **store Upstart scripts**. These scripts will start or stop different services based upon different events, including going to a specific runlevel.

If an administrator wants to change the runlevels of a service, the configuration file for that service can be modified in the `/etc/init` directory. Within the `/etc/init/SERVICE` file should be two lines which define the runlevels to start and stop.

```
/etc/init.d/apache2.conf
start on runlevel [2345]
stop on runlevel [!2345]
```

**To disable a service without uninstalling it, an override file can be created** in the <mark style="color:red;">**`/etc/init/`**</mark> directory. This file should have the same name as the service configuration file, but ending in **`.override`** instead of **`.conf`** .This is the preferred technique over commenting out the "start on" lines.

The contents of the `.override` file should simply be the word `manual` ,which means that the servie will ignore any "start on" lines from the configuration file.

```
[sysadmin@localhost ~]$ sudo 'echo manual > /etc/init/apache2.override'
```

## The `systemctl` command

The <mark style="color:red;">**`systemctl`**</mark> command is used in **systems that have `systemd`** as a replacement for the traditional `init` process.

The <mark style="color:red;">**`systemctl`**</mark> command **looks in the** **`/usr/lib/systemd`** directory for information about which symbolic links enables a specific service.

It is also possible to edit service files in order to modify the service; however, these changes should be made to service files found in the `/etc/systemd` directory instead.

To manually control the state of a service, use the `systemctl` command to start, stop or check the status of that service.&#x20;

```bash
systemctl start httpd.service
systemctl stop httpd.service
systemctl status httpd.service
```

To view the status of all services:

```bash
systemctl -a
systemctl --all
```

To configure a service to **start automatically**, use:

```bash
systemctl enable httpd.service
```

To configure a service **not start automatically** use:

```bash
systemctl disable httpd.service
```

It is possible to change to a different runlevel with the <mark style="color:red;">**`systemctl`**</mark> command.

```bash
systemctl isolate DESIRED.TARGET
```

The <mark style="color:red;">**`systemctl`**</mark> command can also manage the low or no power states of the system with command lines such as:

```
systemctl hibernate
systemctl suspend
systemctl poweroff
systemctl reboot
```

Similar to the **`chkconfig --list`** command, **all the services that are supposed to be enabled for a specific target** within systemd can be viewed by using a <mark style="color:red;">**`systemctl list-dependencies`**</mark> command for that target, such as:

```bash
[root@localhost ~]# systemctl list-dependencies graphical.target
graphical.target
├─atieventsd.service
├─gdm.service
├─jexec.service
├─systemd-readahead-collect.service
├─systemd-readahead-replay.service
├─systemd-update-utmp-runlevel.service
 ├─abrt-ccps.service
```

{% hint style="info" %}
**Consider This**

Because there are three different types of boot systems, traditional **`init`**, **`Upstart`** and **`systemd`**, the logical question is, "Which one does my system use?" The easy answer to this question is to check for the existence of two directories: the `/etc/init` and the `/etc/systemd` directory.

* If your system has a **`/etc/init` directory,** then your system is using **Upstart**.
* &#x20;If your system has a **`/etc/systemd` director**y, then your system is using **systemd**.
* Otherwise, your system is using traditional **`init`**.
{% endhint %}

```bash
systemctl get-default #shows the default target
systemctl list-units --type=service #list the unit 
systemctl cat nfs-client.target
systemctl list-unit-files #show dependencies enabled and disabled
```

## Boot Target

Many modern systems use the systemd rather than init for setting boot targets.&#x20;

| Runlevel | Purpose                                                                   | systemd Target      |
| -------- | ------------------------------------------------------------------------- | ------------------- |
| 0        | Halt or shut off the system                                               | `poweroff.target`   |
| 1        | Single-user mode for administrative tasks                                 | `rescue.target`     |
| 2        | Multi-user mode without configured network interfaces or network services | `multi-user.target` |
| 3        | Normal startup of the system                                              | `multi-user.target` |
| 4        | User-definable                                                            | `multi-user.target` |
| 5        | Start the system normally with a graphical display manager                | `graphical.target`  |
| 6        | Restart the system                                                        | `reboot.target`     |

To check the current runlevel on a Linux system, list the `/etc/systemd/systemd/default.target` file using the `ls -l` command

```
[root@localhost ~]# ls -l /etc/systemd/system/default.target
lrwxrwxrwx 1 root root 37 Dec  4 14:39 /etc/systemd/system/default.target ->/lib/systemd/system/multi-user.target
```

If you need to set the system to boot into a single-user mode for troubleshooting or recovery operations, use the `systemctl enable rescue.target` command, followed by `systemctl set-default rescue.target` command.

To change the system to graphical mode after booting, use the `systemctl isolate graphical.target` command.

## `acpid`

Linux systems use the Advanced Configuration and Power Interface (acpi) event daemon <mark style="color:red;">`acpid`</mark> to norify user-space programs of ACPI events. The ACPI allows the kernel to configure hardware components and manage the system's power settings, such as battery status monitoring, temperature, and more.

One example of using `acpid` for power management would be having the system shut down after the user presses the power button. On modern systems, `acpid` is normally started as a background process during bootup and opens an event file in the `/proc/acpi` directory.&#x20;

When the kernel sends out an ACPI event, `acpi` will determine the next steps based on rules defined in configuration files in the `/etc/acpi` directory.&#x20;

{% hint style="info" %}
The `acpi` command is used to display information about **system hardware ACPI settings**.&#x20;
{% endhint %}

There are many options available to the <mark style="color:red;">`acpi`</mark> command to display various information for power management.&#x20;

| Option                                                 | Purpose                                                                                         |
| ------------------------------------------------------ | ----------------------------------------------------------------------------------------------- |
| <p><code>-b</code></p><p><code>--battery</code></p>    | Displays battery information                                                                    |
| <p><code>-a</code></p><p><code>--ac-adapter</code></p> | Displays ac adapter information                                                                 |
| <p><code>-t</code></p><p><code>--thermal</code></p>    | Displays thermal information                                                                    |
| <p><code>-c</code></p><p><code>--cooling</code></p>    | Displays cooling device information                                                             |
| <p><code>-s</code></p><p><code>--show-empty</code></p> | Displays non-operational devices                                                                |
| <p><code>-f</code></p><p><code>--fahrenheit</code></p> | Uses Fahrenheit as the temperature unit instead of the default, Celsius                         |
| <p><code>-i</code></p><p><code>--details</code></p>    | Displays additional details if they are available; battery capacity and temperature trip points |

\












\


