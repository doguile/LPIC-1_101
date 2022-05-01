# Module 2: Administering the Display

## Chapter 3: X Window

> **106.1: Install and configure X11 v2**
>
> Weight: 2

<details>

<summary>Key terms</summary>

**`/etc/X11/xorg.conf`** This file contains numerous sections with configuration items for the Xserver.

**`/etc/X11/xorg.conf.d`** This directory contains the `/etc/X11/xorg.conf` file, along with any file with a `*.conf` extension

**`X`** The primeray GUI server used by Linux has historically been based upon the X Windows project, or simply X

**`~/.xsession-errors`** For some desktop environments the output from STDERR is sent to the \~/.xsession.errors log file.

</details>

## Introduction

The primary GUI server used by Linux has historically been based upon the **X Window project**, or simple **X**. The interface provided to the user is called a desktop. This is software that works with the X window system to provide an easy way for the user to interact with the operating system.

## Components

The following are some of the more common components of the X Window system

1. Hardware drivers
2. X server
3. X client
4. Window managers
5. Display manager
6. Widget/toolkit libraries

### Hardware drivers

**The hardware drivers include packages that define graphics adapters and input devices**. Common graphics adapter (also referred to as video card, graphics card, or display card) libraries would include those from vendors such as AMD, Intel, Nvidia, and others. Common input devices, for example, a mouse and keyboard could be vendor-specific such as Apple, Logitech, Synaptic, or VIA. Input types can also be vendor-neutral, such as Bluetooth.

### X Server

The X Window system **uses a server-client model** to manage the GUI on a local machine or a network of computers. **The design of X Window is network-oriented with the X11 protocol** allowing the user to have the X client application, like a terminal application, located either remotely or locally on the same system as the X server.

This, in turn, allows the X client application to talk and make requests for information from the X server, which returns the requested information to the X client. The main executable for the X server is a program simply known as X.

![](<../.gitbook/assets/image (19).png>)

### X Client

Although the concept of an application being a client to a local X server might seem confusing at first, it makes sense when you think that the server is the component that updates the display and handles keyboard and mouse input from the user. The X client could also be running on a remote machine where the screen is being updated with output from an X application running locally.

#### Window Manager

It is the job of the _window manager_ to **manage the placement of windows and determine which window components** (menus, mouse pointers, etc.) **will be visible**. The window manager is also an X client with the special privilege of being able to manage other X clients.

To think of X Window as a complete GUI would be a big mistake. Instead, **X Window defines the X11 protocol for communication and graphic primitives**. This protocol defines only the basic rules by which GUI-based deives communicate.

{% hint style="danger" %}
By design, X Window does not specify what windows, buttons, icons, menus, pointers, login screens, etc.., look like
{% endhint %}

### Display Manager

The display manager component is **responsible for allowing the user to perform a graphical login** making **use of a network login protocol**, the <mark style="color:orange;">**X Display Manager Control Protocol (XDMCP)**</mark>, and starting the window manager.

Typical display managers currently in use include the X Display Manager (**XDM**), the KDE Display Manager (**KDM**), the GNOME Display Manager (**GDM**), and the MDM Display Manager (**MDM**).

![](<../.gitbook/assets/image (4).png>)

Typically, these display managers are included as part of the desktop environment:

1. The GNOME desktop environment provides the `gdm` dislpay manager
2. The KDE desktop environment provides the `kdm` display manager
3. If no desktop environment is installed, the `xdm` display manager is used
4. Some distributions have developed their own display manager. For example manager for Ubunto is called `lightdm`

Each display manager mentioned above also has set of configuration files:

1. The `gdm` configuration files are found in the `/etc/gdm` directory. For some special settings, the `/etc/gdm/custom.conf` file is used.
2. The `kdm` configuration files are found in the `/etc/kdm` directory.
3. The `xdm` configutation files are found in the `/etc/X11/xdm` directory

### Turn the Display Manager On or Off

Whether or not a display manager will be on or off **in most Linux distributions** **depends upon the default runlevel and the distribution**. For Debian-derived distributions, this means that if the default runlevel is anything between 2 and 5, then a display manager will be started automatically if it is installed. For Debian the <mark style="color:red;">`/etc/inittab`</mark> files defines the default runlevel within the second filed of the following line:

```bash
id:2:initdefault:
```

In Red Hat-derived systems, the same line in the `/etc/inittab` files defines the default runlevel, and in runlevel 3, the display manager and graphical environment are not started, but if the default runlevel is 5, then the display manager will be started if all goes well:

```
id:5:initdefault:
```

### Switch Display Managers

In order to switch from one display manager to another, there are couple of techniques, depending on which distribution you use. For Debian-derived distributions, use the <mark style="color:red;">**`dpkg-reconfigure`**</mark> command for a package that provides a display manager. **This allows the choice of which display manager to use by default.**

For example, executing `sudo dpkg-reconfigure kdm` results in the following dialog box:

![](<../.gitbook/assets/image (8).png>)

If the system is a Red Hat-derived Linux distribution, then the <mark style="color:orange;">**`/etc/sysconfig/desktop`**</mark> **file is** **used to set the default display manager and desktop environment**. To use the `gdm` display manager and the GNOME desktop enviroment, this file should be set to contain the following entries:

```bash
DESKTOP="GNOME"
DISPLAYMANAGER="GNOME"
```

To use the `kdm` display manager and the KDE desktop environment, this file should be set to contain the following entries:

```bash
DESKTOP="KDE"
DISPLAYMANAGER="KDE"
```

{% hint style="info" %}
It is not required that the desktop environment match the display manager that is being used. It woudl be possible to use the `kdm` display manager with the GNOME desktop or the `gdm` display manager with the KDE desktop.
{% endhint %}

### Change the Display Manager Greeting

The greeting or banner that is displayed for each display manager can be changed. For the <mark style="color:red;">`gdm`</mark> display manager, **the settings for the banner are controlled by modifying an XML file**. Rather than editing this file directly, the <mark style="color:red;">**`gconftool-2`**</mark> **is recommended for making changes**.

For the `kdm` display manager, a banner or greeting can be set if the theme is disabled. An administrator can make changes to the /`etc/kde/kdm/kdmrc` file by modifying the `UseTheme` and `GreetString` keys to the values shown in the following:

```bash
UseTheme=false
GreetString=Message to display in banner
```

### Change Default Color Depth

**The color depth for the display manager determines the maximum number of colors that can be displayed on the screen**. Within the Screen section of the <mark style="color:red;">`/etc/X11/xorg.conf`</mark> file, the <mark style="color:orange;">**`DefaultDepth`**</mark> should be **set to a value that can be supported** by the video card and monitor.

The number that is specified is actually the exponent of the number 2, so a default deph of 16 indicates that 2^16 numbers of colors (or 65.536 colors) can be displayed.

```bash
Section       "Screen"
⁠⁠ 
Identifier    "Default Screen"
Monitor       "Monitor"
Device        "Video Card"
DefaultDepth  16
Modes         "1024x768"
EndSection
```

### Widget/Toolkit libraries
