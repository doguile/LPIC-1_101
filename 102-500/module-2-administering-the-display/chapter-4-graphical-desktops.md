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

Xfce is a lightweight desktop environment. The advantages of using Xfce are load time and responsiveness, while the disadvantages are graphical effects and transparency. This makes Xfce ideal for older computers with less RAM and slower video cards, and those who value performance.

## Installing the Desktop Environment

