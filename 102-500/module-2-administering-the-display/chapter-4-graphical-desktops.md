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



****

****

****

****

****

****

****

****

****

















