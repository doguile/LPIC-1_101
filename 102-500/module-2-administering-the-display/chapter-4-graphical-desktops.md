---
description: '106.2: Graphical Desktops v2  Weight: 1'
---

# Chapter 4: Graphical Desktops

<details>

<summary>Key terms</summary>

<mark style="color:red;">`DISPLAY`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The DISPLAY variable can be **used to run graphical programs on a server** once connectivity to a remote machine has been established.

<mark style="color:red;">`Gnome`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The GNOME Desktop is one of the **most popular Desktop Environments** that runs on the Linux Operating Systems and provides a GUI.

<mark style="color:red;">`KDE`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> The KDE (K Desktop Environments), named as a wordplay on the UNIX-based CDE (Common Desktop Environments), has a very rich suite of programs and applications. KDE is mostly written in C++ and Qt

<mark style="color:red;">`RDP`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> RDP (Remote Desktop Protocol) is a propietary protocol developed by Microsoft for use with its Remote Desktop Connection software included with Windows. In order to interface with Windows systems, Linux systems are able to use various clients, including `rdesktop` and `xfreerdp`

<mark style="color:red;">`Spice`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Spice is an open source guest, server ,protocol, and client; the guest is an interface to a kernel virtual machine (KVM)

<mark style="color:red;">`VNC`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> VNC is an open source, platform-independent **graphical desktop sharing program**. It relies upon the Remote Frame Buffer protocol to **transmit graphics across a network to a remote host.**

<mark style="color:red;">`X11`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Refers to the **X** (Windows X) **protocol version 11** which has been forked to the Xorg Project. However the term 11 is still used to refer to the process of sending graphics over a network connection.

<mark style="color:red;">`XDMCP`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Is a protocol built into Xorg, **used to share a remote screen**, similar to X11-Forwarding over SSH.

<mark style="color:red;">`Xfce`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Xfce is a **lightweight desktop environment**

<mark style="color:red;">`xauth`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> For an X server to be able **to forward graphical programs to another computer**, an **authorization file** must be referred to. The `xauth` command generates an MIT Magic Cookie which is typically stored in a hidden file called `.Xauthority`&#x20;

<mark style="color:red;">`xhost`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> In order to **allow a connection from a remote machine**, the `xhost` command can be **executed to add a host to be permitted**.

</details>

## Introduction

To meet the need for application productivity, many distributions also come with a graphical user interface (GUI). The graphics themselves are actually built upon several layers, a display server, a display manager, a window manager, and a desktop environment.

From the truncated output of the `pstree` command, we can ascetain that the display manager in the following example is Xorg, the display manager is lightdm, and the desktop environment is Cinnamon.

```bash
root@localhost:~# pstree | grep dm
    	|-lightdm-+-Xorg---{Xorg}
    	|     	|-lightdm-+-cinnamon-sessio-+-applet.py---{applet.py}
    	|     	|     	`-2*[{lightdm}]
    	|     	`-2*[{lightdm}]
```

The desktop environment is the component that users tend to interact with.

## Desktop Environments

### GNOME

The GNOME Desktop and its derivatives are the default desktop for some of the world's most popular Linux distributions, including Debian, Fedora, Ubuntu, and Mint. Majors supporters of the GNOME project include Red Hat (IBM), Cannonical, and Google.

GNOME components are developed in many languages, with the GTK (GNOME Tool Kit) used for widgets.

### KDE

The KDE (K Desktop Environment) has been constant development since 1996; therefore, it has a very rich suite of programs and applications. These include Amarok (music player), Krita (paint app), Konqueror (browser), DigiKam (photo gallery), and many more.

KDE is mostly written in C++ and Qt (pronounced “cute”). [Qt Designer](https://doc.qt.io/qt-5/qtdesigner-manual.html) is an IDE (Integrated Desktop Environment) that is used to build KDE applications efficiently.

### Xfce

Xfce is a **lightweight desktop environment**. The advantages of using Xfce are load time and responsiveness, while the disadvantages are graphical effects and transparency. This makes Xfce ideal for older computers with less RAM and slower video cards, and those who value performance.

## Installing the Desktop Environment

The different distributions often make a _meta-package_, or group of packages, to accomplish this task. For Debian-based distributions, there is the `gnome-session` package, which is a meta-package containing the full suite of GNOME applicactions and components. This can save an administrator time and effort in selecting the individual application packages to install along  with the `desktop-base` and `gnome` packages.

For example, if an administrator wanted to install the X Windows system and the GNOME desktop environment on a Debian-based system that didn't have X Windows previously installed, the following command would accomplish this task:

```bash
sysadmin@localhost:~$ sudo apt-get install xserver-xorg gnome-session
```

> The packages being installed, `xserver-org` and `gnome-session` ,refer to the X Windows system and GNOME desktop environment, respectibely.

To install the X Windows system and the KDE desktop environment on a Debian-based distribution, execute the following command:

```bash
sysadmin@localhost:~$ sudo apt-get install xserver-xorg kde-standard
```

For **Red Hat-derived** distributions, there is a package group for the **GNOME desktop environment**, which could be installed by an administrator with the following command:

```bash
sysadmin@localhost:~$ yum groupinstall general-desktops
```

To install the package group for the KDE desktop environment on Red Hat-derivded distributions, an administrator can execute the following command:

```
sysadmin@localhost:~$  yum groupinstall kde-desktop
```

### Desktop Localization

The Debian-derived KDE package name for a particular language follows the form of **`kde-i18n-`**_**`locale-code`**_. For example, the Debian package for Spanish is `kde-i18n-sp` ,and it could be installed by an administrator with the following command:

```bash
sysadmin@localhost:~$ sudo apt-get install kde-i18n-sp
```

For Red Hat-derived distributions, the KDE localization package name follows the form of **`kd3-i18n-`**_**`locale`**_. So the package for Spanish users could be installed with the following command:

```bash
sysadmin@localhost:~$ sudo apt-get install kde-i18n-Spanish
```

Successful installation of any of the above packages should result in a ready to use graphical environment, including the X server and chosen desktop environment

The <mark style="color:red;">`localepurge`</mark> tool can be used to remove localization files for languages that you do not use. This can free up considerable space on the hard drive. To install and use the `localepurge` tool on a Debian-based system, use the following command:

```bash
root@localhost:~ apt install localepurge; dpkgreconfigure localepurge
```

The terminal will turn blue, revealing a pseudo-graphical interface. Select the languages that you want to keep by using the keyboard arrows and the **Spacebar**. ****&#x20;

![](<../../.gitbook/assets/image (22).png>)

## **Remote Desktop Environments**

#### **X-11 Forwarding**

The Xorg project was forked from the X11 project many years ago; however, **we still refer to sending graphics over a network connection as X11-Forwarding**.

Xorg can pipe graphics over `ssh` ,so the first command needed to get remote graphics working is the following for Debian-based machines:

```bash
root@remoteserver:~ apt install openssh-server
```

Or for Red Hat-based machines:

```
root@remoteserver:~# dnf install openssh-server
```

From the remote machine, verify that an `ssh` connection can be made.

```bash
sysadmin@localhost:~$ ssh remoteserver@192.168.0.7
The authenticity of host '192.168.0.7 (192.168.0.7)' can't be established.
ECDSA key fingerprint is SHA256:uaE/DovZplLYBbTZoEGNtoBf+WQfxJ0zx3UbU2cG2uI.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.0.7' (ECDSA) to the list of known hosts.
remote@192.168.0.7's password:
Last login: Sun Dec 22 11:37:24 2019 from 192.168.0.7
remote@server ~ $
```

Once connectivity has been established, graphical programs can be run on the server by invoking them with the <mark style="color:red;">`DISPLAY`</mark> variable.

```bash
remote@server ~ $ DISPLAY=0 gnome-calculator
```

Unfortunately, the graphics will appear on the remote server while the client sees nothing. Close the program, exit the `ssh` session, and try again, this time using the <mark style="color:red;">**`-X`**</mark> option.

```bash
remote@server ~ $ exit
logout
Connection to 192.168.0.7 closed.
sysadmin@localhost:~$ ssh -X remote@192.168.0.7
remote@192.168.0.7's password:
Last login: Sun Dec 22 12:37:31 2019 from 192.168.0.7
remote@server ~ $ gnome-calculator
```

This time, the `DISPLAY` variable does not need to be used, and the graphical program appears on the local computer.&#x20;

> Remote forwarding in this fashion is very useful for thin clientes such as kiosks and cash registers.

{% hint style="warning" %}
To view and modify various <mark style="color:red;">`ssh`</mark> settings on your server, including X11-Forwading, refer to the **`/etc/ssg/sshd_config`** file.
{% endhint %}

For an X server to be able to forward graphical programs to another computer, an authorization file must be referred to. Fortunately, this is taken care of during an <mark style="color:red;">`ssh`</mark> connection. The program which accomplishes this is called <mark style="color:red;">`xauth`</mark> ,which generates an MIT Magic Cookie. **This cookie is typically stored in a hidden file called **<mark style="color:red;">**`.Xauthority`**</mark>** **&#x20;

{% hint style="info" %}
Any computer that has a copy of this cookie is allowed to remotely run graphical programs generated by the X server.
{% endhint %}

```bash
sysadmin@localhost:~$ xauth -v
Using authority file /home/sysadmin/.Xauthority
xauth> ?
Commands:
    	add   	exit  	extract   help  	info  	list 	 
    	merge 	nextract  nlist 	nmerge	quit  	remove    
    	source	version   ?     	generate
xauth> exit
```

This file is data, not text; therefore the output of this fiel appears to be corrupted, but the words MIT MAGIC-COOKIE can be still discerned

```bash
sysadmin@localhost:~$ file .Xauthority
.Xauthority: data
sysadmin@localhost:~$cat .Xauthority
localhost.localdomain0MIT-MAGIC-COOKIE-1R����b���p�s:�p1MIT-
```

## Using a Remote X Server

One of the benefits of the X Server is the ability to run a GUI-based program on one system and have its output displayed on another system. **To properly set up a server and client, two things must be set** correctly in order for this functionality to work: the <mark style="color:red;">**`xhost`**</mark> settings and the <mark style="color:red;">**`DISPLAY`**</mark> environment variable.

By default, the X Server will permit connections from clients that are from the same host (known as `localhost`). In order to allow a connection from a remote machine, the <mark style="color:red;">**`xhost`**</mark> command can be **executed to add a host to be permitted**.&#x20;

For example, to permit connections on your local system from two systems, one with a resolvable hostname `server1` and the other with an IP address of `192.168.20.30` ,execute the following command:

```bash
root@localhost:~ xhost +server1 +192.168.20.30
server1 being added to access control list
192.168.20.30 being added to access control list
```

To restrict access to the X server for `server1` ,remove the host by using the <mark style="color:red;">**`xhost`**</mark> as follows:

```bash
root@localhost:~ xhost -server1
server1 being removed from access control list
```

To verify which host are currently allowed, execute the `xhost` command without any arguments:

```bash
root@localhost:~# xhost
access control enabled, only authorized clients can connect
INET:192.168.20.30
SI:localuser:root
```

The syntax for the output of the previous `xhost` command includes the family and the system name in the form of `family:name` .The family name, which is not case-sensitive, is essentially a category which can be one of the following

```bash
NAMES
 
       A complete name has the syntax ''family:name'' where the	 families  are
       as follows:
 
       inet	 Internet host (IPv4)
       inet6	 Internet host (IPv6)
       dnet	 DECnet host
       nis	 Secure RPC network name
       krb	 Kerberos V5 principal
       local	 contains only one name, the empty string
       si	 Server Interpreted
```

While troubleshooting problems with the `xhost` settings, disable the enforcement of any security by allowing all hosts with the following command.

```bash
root@localhost:~ xhost +
access control disabled, clients can connect from any host
```

> Note the output of this command clearly indicates that now any host can display graphic output on the local machine. While this may aid in troubleshooting `xhost` problems, it is not good practice to use this setting permanently

To enable enforcement of security, execute the following:

```
root@localhost:~#  xhost -
```

When using the <mark style="color:red;">`xhost`</mark> to forward graphics from an X server to a remote computer, <mark style="color:red;">**`xauth`**</mark> **must be manually invoked to generate an MIT Magic Cookie.**

## Connecting to a Remote Display

This section will outline the process of connectiong to a remote display:

1. The first step in attempting to use an X server remotely will be to **permit X connections from a remote host back to the originating X server** by adding the host with the <mark style="color:red;">`xhost`</mark> command. For example, to connect to a system with a name that can be resolved to a name of `centos` from the system named `server1` , execute the following command on `server1`

```
root@localhost:~# xhost +centos
```

2\. The second step is to make the connection to the remote host with the `ssh` or `telnet` command.

3\. The third stop would be set the `DISPLAY` environment variable. In some cases, the value of the variable may be appropriately set automatically and will not need to be changed. If, however, the `DISPLAY` variable contains a hostname that does not resolve back to the address of the system where the `ssh` session originated `server1`, then it will have to be set. For example, when th following command is executed through the remote connection logged into `centos`

```
centos:~$ echo $DISPLAY
```

If the output of this command was:

```
localhost.localdomain:0.0
```

This means that the X client should try to use the localhost system's first display (0.0). Since `localhost` in this context refers to the host that you have connected to (not the remote system running the X server) this setting will not work correctly. On the other hand, if the output of `echo $DISPLAY` is as show below, then GUI-based output will be sent to the remote machone, `server1`

```
server1.test:0.0
```

To set the `DISPLAY` variable manually, execute the following command:

```
root@localhost:~ export DISPLAY="server1.test:0.0"
```

4\. The final step would be to verify that an X client would be able to connect back to the X server where the `ssh` session originated. Executing almost any graphical application would work at this step.

```
centos:~$ xclock
```

If a graphical clock appears on the remte X system (`server1`), then it is working; otherwise, if output appears like below, then there is a problem:

```
Error: Can't open display: localhost.localdomain:0.0
```

Keep in mind that even with a correctly configured `xhost` and `DISPLAY` variable, a remote GUI-based display may not work. Other issues could be interfering:

* Firewall settings on both systems
* Host access restrictions in the `/etc/hosts.allow` and `/etc/hosts.deny` files.
* X server settings on the system that is supposed to accept a remote X connectin; for instance , the `/etc/ssh/sshd_config` file

{% hint style="info" %}
In order to configure a **Red Hat-derived** system with a default installation to accept incoming connections from remote systems to the X server, the **`/etc/gdm/custom.conf`** file must have the following under the `[security]` heading:

```
DisallowTCP=false
```
{% endhint %}

### XDMCP

















