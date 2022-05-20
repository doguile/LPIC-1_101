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

GUIs are intended to provide a user with a visual way of issuing commands to the computer through the use of **Windows, Icons, Menus and Pointers (WIMP)**. As opposed to the Commnad Line Interface (CLI) or shell, which is the way commands traditionally been issued to the operating system.

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

The X Window system **uses a server-client model** to manage the GUI on a local machine or a network of computers. **The design of X Window is network-oriented with the **_**X11 protocol**_ allowing the user to have the X client application (like a terminal application), located either remotely or locally on the same system as the X server.

This, in turn, allows the X client application to talk and make requests for information from the X server, which returns the requested information to the X client. The main executable for the X server is a program simply known as X.

![](<../../.gitbook/assets/image (19).png>)

### X Client

Although the concept of an application being a client to a local X server might seem confusing at first, it makes sense when you think that the server is the component that updates the display and handles keyboard and mouse input from the user. **The X client could also be running on a remote machine where the screen is being updated with output from an X application running locally**.

#### Window Manager

It is the job of the _window manager_ to **manage the placement of windows and determine which window components** (menus, mouse pointers, etc.) **will be visible**. The window manager **is also an X client** with the special privilege of being able to manage other X clients.

To think of X Window as a complete GUI would be a big mistake. Instead, **X Window defines the X11 protocol for communication and graphic primitives**. This protocol defines only the basic rules by which GUI-based deives communicate.

{% hint style="danger" %}
By design, X Window does not specify what windows, buttons, icons, menus, pointers, login screens, etc.., look like
{% endhint %}

### Display Manager

The display manager component is **responsible for allowing the user to perform a graphical login** making **use of a network login protocol**, the <mark style="color:orange;">**X Display Manager Control Protocol (XDMCP)**</mark>, and starting the <mark style="color:orange;">**window manager**</mark>.

Typical display managers currently in use include the X Display Manager (**XDM**), the KDE Display Manager (**KDM**), the GNOME Display Manager (**GDM**), and the MDM Display Manager (**MDM**).

![](<../../.gitbook/assets/image (4).png>)

Typically, these display managers are included as part of the desktop environment:

1. The GNOME desktop environment provides the **`gdm`** dislpay manager
2. The KDE desktop environment provides the **`kdm`** display manager
3. **If no desktop environment is installed, the `xdm` display manager is used**
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

![](<../../.gitbook/assets/image (8).png>)

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

For the <mark style="color:red;">`kdm`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> display manager, a banner or greeting can be set if the theme is disabled. An administrator can make changes to the /`etc/kde/kdm/kdmrc` file by **modifying the** <mark style="color:orange;">**`UseTheme`**</mark>** and **<mark style="color:orange;">**`GreetString`**</mark><mark style="color:orange;">** **</mark><mark style="color:orange;">****</mark>** keys** to the values shown in the following:

```bash
UseTheme=false
GreetString=Message to display in banner
```

To make these changes effective, the administrator will need to restart the X server or restart the system.

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

Typically, **a windows manager will use one library or widger toolkit for all of its widgets**: icons, buttons, checkboxes, sliders, etc. Widget toolkits include the old and one propietary Motif (which was standardized by the Common Desktop Enviroment used by UNIX), and the very common Gtk+ (GIMP toolkit) used for C development of graphical applications.

To aid in making this task easier, pre-tested combinations of components are available, which are known as Desktop Environments. Two of the most popular desktop environments are:

* GNOME
* KDE

One of the benefits of installing the desktop manager with a desktop environment is that it will come bundled with a number of programs that use the same library for their widgets. As a result, all of the programs will show the same appearance and usability.

The state of the Graphical User Interface (GUI) for Linux is always evolving. Thirty years after the introduction of the X Window project at the Massachusetts Institute of Technology (MIT), the future for the X11 protocol is uncertain.

The Wayland protocol seems to be one of the more promising alternatives to X. Although the reference implementation of the Wayland Protocol, known as Weston, could be possibly replace the X server, the protocol currently allows for one or more X servers to be used for backward compatibility.

## Configuring X Windows

There are many types of hardware that may be used in an X Windows configuration:

* Video cards
* Display monitors
* Keyboards
* Mice
* Pointing devices

The video card used is the most critical piece of hardware to be detected because if it doesn't work, then it won't be possible to see if anything else works.

For Debian-derived distributions, starting the system in any runlevel other than single-user mode will automatically attempt to start the X server.  For Red Hat-derived systems, the X server is only started in runlevel 5 by default.

{% hint style="info" %}
Recall that the **X server is responsible for handling updates to the display** **and handles** keyboard and mouse input from the user as well as **communicating with X clients (application)**
{% endhint %}

If there is any question about the hardware changes to the computer being compatible with X Windows, then running in a non-graphical mode is recommended.

To start a single-user mode, an administrator can press a key at boot time when the GRUB menu appears, select to Edit the kernel parameters, and add one of the following values: `1` ,`s`, `S`, or `single`. The resulting kernel line may appear something like the following with the runlevel specified as the final parameter:

```
kernel /boot/vmlinuz-3.1.25 root=/dev/sda2 ro 1
```

If the system is already running, you can attempt to access an alternative consolo by pressing **Ctrl+Alt+F2**. Provided the previous task was successful, it should be possible to log in the system via the command line

After loggin in to the system, switch to runlevel 1 by executing the following with root privileges:

```bash
root@localhost:~ telinit 1
root@localhost:~ startx
```

Normally, most systems only configure the X server for one display; the identifier for this display will be referred to as `0`. If additional displays are configured, they are numbered incrementally, so a second display would be referred to as `1`.

**If the display doesn't start or acts strangely, then the log file for the display can be viewed: the **<mark style="color:orange;">**`/var/log/Xorg.0.log`**</mark> file for display 0, the <mark style="color:orange;">**`/var/log/Xorg.1.log`**</mark> file for display 1, etc. Lines within these files are tagged with the following letters to indicated their importance:

* `II` = Informational
* **`WW`** = **Warning**
* **`EE`** = **Error**
* `**` = From the configuration file
* `++` = From the command line
* `--` = Probed
* `NI` = Not implemented
* `??` = Unknown

Before describing typical errors in starting the X server and their causes, it will be helpful to understand what the `/etc/X11/xorg.conf` file contains.

### `/etc/X11/xorg.conf` File

The `/etc/X11/xorg.conf` file, along with any file with a `*.conf` extension in the `/etc/X11/xorg.conf.d/` directory, contains numerous sections with configuration items.

{% hint style="danger" %}
The most recent versions of the X server can actually run without any `xorg.conf` file as they automatically configure themselves when started
{% endhint %}

But an `xorg.conf` may still be used to create custom configurations. The following is a list of the sections that are defined for this file:

| Section        | Purpose                                                  |
| -------------- | -------------------------------------------------------- |
| `Files`        | File pathnames for fonts and modules                     |
| `ServerFlags`  | Server flags are global options                          |
| `Module`       | Dynamic module loading of modules that extend the server |
| `Extensions`   | Extension enabling for the X11 protocol                  |
| `InputDevice`  | Input device description for keyboard and pointers       |
| `InputClass`   | Input class description                                  |
| `Device`       | Video card device description                            |
| `VideoAdaptor` | Xv video adaptor description                             |
| `Monitor`      | Monitor description                                      |
| `Modes`        | Video modes descriptions                                 |
| `Screen`       | Screen configuration                                     |
| `ServerLayout` | Overall layout combining other sections                  |
| `DRI`          | Direct Rendering Infrastructure                          |
| `Vendor`       | Vendor-specific configuration                            |

## Troubleshooting the X Server

The [http://www.x.org/wiki/FAQErrorMessages](https://www.x.org/wiki/FAQErrorMessages/) web page describes some common error messages seen when attempting to start the X server. The following examples describe some common errors and how to **troubleshoot them based upon this information by modifying the `/etc/X11/xorg.conf` file.**

### **MIssing Configuration File**

If a system does not currently have an `/etc/X11/xorg.conf` file or to create a new configuration file, generate it using the command:

```bash
root@localhost:~# X -configure
```

If the X server has not been stopped, then an error message similar to the following will appear:

```bash
Fatal server error:
Server is already active for display 0
If this server is no longer running, remove /tmp/.X0-lock
⁠⁠ 
and start again.
```

If the X server was not running when the command was executed, then a file named `xorg.conf` new should be generated in the current directory.

To test if this newly-generated file will wokr, first make a backup copy of the `/etc/X11/xorg.conf` file, then move the `xorg.conf` file to the `/etc/X11/xorg.conf` file and start the X server:

```bash
root@localhost:~ cp /etc/X11/xorg.conf /etc/X11/xorg.conf.backup
root@localhost:~ mv xorg.conf.new /etc/X11/xorg.conf
root@localhost:~ startx
```

### No Video Card Detected

This error indicates that the video card device specified in the `Device` section of the file may not be compatible with the video card in your system.

{% hint style="info" %}
If your video card is made by Nvidia or AMD, then it may not work with the OPen Source driver from your Linux distribution.
{% endhint %}

**If your video card will not work with a propietary driver, then it may be possible to use a generic driver known as **<mark style="color:orange;">**`vesa`**</mark>, which can drive most video cards that are compatible with VESA (Video Electronics Standards Association) standards.

The output demonstrates the `Drive` section of the `/etc/X11/xorg.conf` file that has been configured to use the generic `vesa` driver.

```bash
Section "Device"
    Identifier  "Card0"
    Driver      "vesa"
EndSection
```

### No Display Monitor Detected

The video display monitor is almost as crucial to a functioning X window system as the video card. If the screen stays dark and the X Windows cursor never appears, the problem is likely the video card, the display monitor, or both.

When the video card and display monitor are unable to sync together, the error message from the log file will be something like the following:

```
(EE) Screen(s) found, but none have a usable configuration.
```

There are many factor that can limit the compatibility between a video card and a monitor:

1. Horizontal sync rate
2. Vertical refresh rate
3. Color depth
4. Resolution

It is especially likely to have problems when combining a very old video card with a very new monitor, or vice versa.

### Fonts Unavailable

An error message like `font` named `"fixed" cannot be found or loaded` is fatal because if a required font cannot be loaded, then the X server is unable to start.&#x20;

> This error message was more common years ago before fonts were built-in to the X server.

It was previously necessary to ensure that the paths to the fonts on the system were properly configured in the `Files` sectino of the `/etc/X11/xorg.conf` file, which would look something like:

```bash
Section "Files"
    FontPath   "/usr/X11R6/lib/X11/fonts/misc/"
    FontPath   "/usr/X11R6/lib/X11/fonts/Speedo/"
    FontPath   "/usr/X11R6/lib/X11/fonts/Type1/"
    FontPath   "/usr/X11R6/lib/X11/fonts/CID/"
    FontPath   "/usr/X11R6/lib/X11/fonts/75dpi/"
    FontPath   "/usr/X11R6/lib/X11/fonts/100dpi/"
EndSection
```

Another problem that may have occurred in the past was that a separate X font service <mark style="color:red;">`xfs`</mark> was used to provide the fonts for the X server. **If the **<mark style="color:red;">**`xfs`**</mark>** service was not started at the time the X server was started, then fonts would also be unavailable**.

If the `xfs` is in use, then the `Files` section of the `/etc/X11/xorg.conf` file may appear with an entry that refers to a Unix socket with `"unix:/7100"` specified as the FontPath:

```bash
Section "Files"
   ModulePath   "/usr/lib/xorg/modules"
FontPath        "unix/:7100"          # local font server
# if local font server has problems, fall back on these
FontPath        "/usr/lib/X11/fonts/misc"
FontPath        "/usr/lib/X11/fonts/cyrillic"
FontPath        "/usr/lib/X11/fonts/75dpi/"
FontPath        "/usr/lib/X11/fonts/100dpi/"
FontPath        "/usr/X11R6/lib/X11/fonts/sgi"
FontPath        "/usr/lib/X11/fonts/Type1"
FontPath        "/usr/lib/X11/fonts/CID"
FontPath        "/usr/lib/X11/fonts/100dpi:unscaled"
FontPath        "/usr/lib/X11/fonts/75dpi:unscaled"
EndSection
```

Because **the X server now has its own built-in fonts for the required fonts**, the font error mentionend previously may not occur. Be familiar with what the `Files` section looks like for a current X server, which does not use an <mark style="color:red;">`xfs`</mark> service:

```bash
Section "Files"
ModulePath   "/usr/lib/xorg/modules"
FontPath     "catalogue:/etc/X11/fontpath.d"
⁠⁠ 
FontPath     "built-ins"
EndSection
```

Systems that do not use the `xfs` server use a newer way of finding fonts known as <mark style="color:red;">**`FontConfig`**</mark>. **To install a new font for all users on a system that uses **<mark style="color:red;">**`FontConfig`**</mark>** ,the font files should be copied to a subdirectory of the `/usr/share/fonts` directory.** Subsequently, the administrator can execute the <mark style="color:red;">**`fc-cache`**</mark> command to **update the font information cache of the system**.

For example, if new fonts are copied to the `/usr/share/fonts/local` directory, then it is possible to execute the command:

```
root@localhost:~# fc-cache /usr/share/fonts/local
```

For fonts files to be installed for an individual user, the fonts can be copied to a `.fonts` directory in the user's home directory. Then, the user can update their font information cache by executing the following command:

```bash
root@localhost:~# fc-cache ~/.fonts
```

### `~/.xsession-errors`

When appplications are started via an icon in X Windows vs the command line, output going to standard error (STDERR) can no longer display on the screen. For some desktop environments (such as GNOME), the output from STDERR is sent to the `~/.xsession-errors` log file, which can be used to troubleshoot problems encountered.

The STDERR output from X Windows and the application running through X Windows are both sent to the `~/.xsession-error` file, so it is worth looking to see if `~/.xsession-errors` exists in your home directory when troubleshooting any X Window problems.



