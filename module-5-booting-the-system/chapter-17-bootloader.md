---
description: >-
  101.2: Boot the system v2  Weight:
  3                                                                                                
  102.2: Install a boot manager v2 Weight: 2
---

# Chapter 17: Bootloader

<details>

<summary>Key terms</summary>

`grub-install` Utility to install GRUB on the hard drive

`grub-mkconfig` Utility used to generate a configuration file for GRUB

`grub.cfg` GRUB configuration file

`grub.conf` GRUB configuration file on **Red Hat Linux**

`menu.lst` Configuration file for GRUB Legacy

</details>

## Introduction

The two bootloaders most commonly used with Linux are the **Grand Unified Bootloader (GRUB)**, or the **Grand Unified Bootloader 2 (GRUB 2)**.&#x20;

{% hint style="danger" %}
Previously, the **Linux Loader (LILO)** was the one most implemented, but it has fallen out of wide usage and is present on older legacy systems.
{% endhint %}

GRUB is a GNU project with a goal of making a bootloader that can boot many different kernels on a variety of systems. GRUB can read filesystems and dynamically learn about the hardware.

As a dynamic bootloader, GRUB also offers a CLI interface that can override the settings from the configuration file. The most typical use of this is to boot into a rescue mode by passing extra kernel parameters.

When a distribution is installed, a bootloader is usually installed automatically, reflecting the hardware particulars for the system. Typical customizations at the point of install are to add extra operating systems for multi-booting.

{% hint style="info" %}
A major difference between GRUB/GRUB2 and LILO is that the LILO bootloader needs to be upgraded after changes are made to configuration files.
{% endhint %}

## GRUB Legacy Bootloader

The older version of GRUB is now called GRUB Legacy.

## GRUB Legacy Boot Steps

1. GRUB Legacy typically writes the _**stage 1 bootloader**_ to the Master Boot Record (MBR).
2. The _**stage 1.5 loader**_ load up the filesystem drivers needed to load the _**stage 2 loader**_ from the <mark style="color:red;">`/boot/grub`</mark> location.
3. The GRUB Legacy bootloading stages will then turn over the rest of the system initialization to the <mark style="color:red;">`/sbin/init`</mark> or **systemd** equivalent for the continuation of the system boot.

## GRUB Legacy configuration

In GRUB Legacy, the first disk detected is referred to as `hd0` ,the second disk as `hd1` and so on. Partitions on disks are also numbered starting at zero. Therefore, use `hd0,0` to refer to the first partition on the first disk, `hd1,0` for the first partition on the second disk.

{% hint style="info" %}
Not only does GRUB Legacy not distinguish between SATA and IDE drives, it also doesn't number drives with letters like the operating system does. For example, the first hard drive is `0`, not `a`.
{% endhint %}

The GRUB Legacy bootloader is typically installed as part of the installation process. In the event that the GRUB Legacy bootloader needs to be reinstalled, then GRUB Legacy can be reinstalled by running the following command:

```bash
grub-install '(hd0)'
```

The `grub-install` command will install the GRUB Legacy bootloader to the first disk device in the system.&#x20;

> The `grub-install` command exists primarily to gather information about the system, its disk, filesystems, and the location of the `/boot/grub` directory on disk, and then install the GRUB Legacy bootloader's image

{% hint style="success" %}
GRUB Legacy can have either or both of the following two main configuration files: the `/boot/grub/menu.lst` and `/boot/grub/grub.conf` files. Depending on the distribution, one file is preferred over the other.&#x20;

For the sake of clarity and reducing confusion, this section will focus on the newer and more common file location, `/boot/grub/grub.conf` file.
{% endhint %}

### `grub-mkconfig`

The <mark style="color:red;">`grub-mkconfig`</mark> command will query the running system for the contents of the `/boot` directory, the contents of the `/etc/default/grub` location if it exists, and any scripts that are located in the `/etc/grub.d` directory.

Next, it will output the properly-formatted configuration file to the specified location. For example, to generate the `/boot/grub/grub.cfg` file, you would use the following command:

```
sysadmin@localhost:~$ grub-mkconfig -o /boot/grub/grub.cfg
```

The `/boot/grub/grub.cfg` file will **contain the gathered information formatted accordingly** and should be **what the system needs to boot the currently installed system** properly.

While the location of grub.conf is the `/boot/grub/grub.conf` file, on some systems a symbolic link named `/etc/grub.conf` makes it easier for the admin to find

```
sysadmin@localhost:~$ ls -l /etc/grub.conf
lrwxrwxrwx 1 root root 22 Jun 18  2012 /etc/grub.conf -> ../boot/grub/grub.conf
```

{% hint style="warning" %}
Keep in mind that only the root user can modify the `/boot/grub/grub.conf` file
{% endhint %}

A typical `/boot/grub/grub.conf` file would look something like this

```bash
#global options
default=0
fallback=1
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
password notencypted

#bootable title sections
title CentOS (2.6.32-358.6.1.el6.i686)
	root (hd0,2)
	kernel /vmlinuz-2.6.32-358.6.1.el6.i686 ro root=/dev/mapper/vg_livecd-lv_root rd_NO_LUKS LANG=en_US.UTF-8 rd_LVM_LV=vg_livecd/lv_swap rd_NO_MD rd_LVM_LV=vg_livecd/lv_root SYSFONT=latarcyrheb-sun16 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet
	initrd /initramfs-2.6.32-358.6.1.el6.i686.img
title CentOS (2.6.32-358.2.1.el6.i686)
	password --md5 $1$D20Ia1$iN6djlheGF0NQoyerYgpp/
	root (hd0,2)
	kernel /vmlinuz-2.6.32-358.2.1.el6.i686 ro root=/dev/mapper/vg_livecd-lv_root rd_NO_LUKS LANG=en_US.UTF-8 rd_LVM_LV=vg_livecd/lv_swap rd_NO_MD rd_LVM_LV=vg_livecd/lv_root SYSFONT=latarcyrheb-sun16 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet
	initrd /initramfs-2.6.32-358.2.1.el6.i686.img
title OtherOS (Windows)
	rootnoverify (hd0,0)
	chainloader +1
```

Global options appear first, followed by title blocks, which contains at least three directives: `root`, `kernel`, `initrd` .These title blocks define an operating system to boot, as well as options to the kernel that indicate how to boot the operating system.



| Directive      | Meaning                                                                                                                                                                                                                                                                                                                              |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `default=`     | Specifies the title to attempt to boot by default after the timeout number of seconds has passed.                                                                                                                                                                                                                                    |
| `fallback=`    | Specifies the title to attempt to boot if the default title fails to boot successfully.                                                                                                                                                                                                                                              |
| `timeout=`     | Specifies the number of seconds to wait before automatically attempting to boot the default title.                                                                                                                                                                                                                                   |
| `splashimage=` | Specify a background graphic that appears behind the text of the menu.                                                                                                                                                                                                                                                               |
| `hiddenmenu`   | Prevents GRUB Legacy from displaying all but the default bootable title until the user presses a key. If the user presses a key, then all titles are displayed.                                                                                                                                                                      |
| `title`        | The title directive starts a new block of directives that form the directives necessary to boot the system. A title block ends when the next title directive appears or when the end of the file is reached.                                                                                                                         |
| `root`         | Uses the special hard disk syntax to refer to the location of the `/boot` directory.                                                                                                                                                                                                                                                 |
| `kernel`       | This line specifies the kernel image file, followed by all the parameters that are passed to the kernel, such as `ro` for read-only and `root=/path/to/rootfs`.                                                                                                                                                                      |
| `initrd`       | This line should specify an initial ramdisk that matches the version and release of the Linux kernel. This file provides a minimal filesystem during kernel initialization prior to mounting the root filesystem.                                                                                                                    |
| `password`     | This option can be used as either a global option or a title option. When specified globally, this requires the user to submit the specified password before appending, editing, or using the GRUB Legacy command line. As a title option, this requires the user to submit the password before GRUB will attempt to boot the title. |
| `rootnoverify` | This directive is used to specify a bootable partition for a non-Linux operating system.                                                                                                                                                                                                                                             |
| `chainloader`  | Used to specify a path to another bootloader or +1 if the bootloader is located in the first sector of the partition specified by the `rootnoverify` directive.                                                                                                                                                                      |

For an encrypted password, use the `--md5` option for the `password` directive:

```
password --md5 $1$D20Ia1$iN6djlheGF0NQoyerYgpp/. 
```

{% hint style="info" %}
To generate the encrypted password, use the <mark style="color:red;">`grub-md5-crypt`</mark> command or execute the `grub` command and then at the `grub` prompt, type the <mark style="color:red;">`md5crypt`</mark> command
{% endhint %}

### Interacting with GRUB Legacy

One of the most common task performed interactively with GRUB Legacy is to boot to a different runlevel.&#x20;

> A runlevel is a status that defines how many services are currently running on a system.

The single user runlevel is when a system has limited services running and is used only to perform administrative tasks.

In the event that there is a serious system problem, the single user runlevel can be very useful. In this runlevel, only the root user has access to the system, and most system daemons are not running.

## GRUB 2

The new GRUB 2 offer several advantages:

* Dynamically loaded modules
* Non-ASCII characters support
* Ability to boot from partitions inside of Logical Volume Managment (LVM) or RAID services.
* Ability to work with architectures that don't have a PC BIOS

### GRUB 2 Boot steps

1. The _first stage loader_ `boot.img` is either read from the first 440 bytes of the MBR, or alternatively read from the Partition Boor Record (PBR)&#x20;
2. The _second stage loader_ `diskboot.img` is used as the first sector of the third stage loader.
3. The _third stage loader_ `core.img` umcompress itself, accesses its own GRUB 2 kernel and filesystem drivers attempts to access the actual root filesystem's `/boot/grub` directory.
4. If it can access the `/boot/grub` directory on disk, then loads the _fourth stage module_ `normal.mod` which then reads the `/boot/grub/grub.cfg` file and presents either a GUI for GRUB 2 or the CLI menu so the user can choose an image to boot.

### GRUB 2 Configuration

Although both Fedora and Ubuntu distributions are now using GRUB 2,they each use a different location for the main configuration file. Fedora uses the `/boot/grub2/grub.cfg` file instead of the `/boot/grub/grub.cfg` file that is used by Ubuntu-based distributions.

{% hint style="warning" %}
Any changes made it to it will be lost upon updating the kernel or if and administrator executes the `update-grub` command (for Ubuntu-based distributions) or the `grub2-mkconfig` command (for Fedora-based systems).
{% endhint %}

While GRUB Legacy expects the main configuration file will be edited, GRUB 2 expects any customizations to occur in the `/etc/grub.d` directory or the `/etc/default/grub` file.

|      File Location     | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| :--------------------: | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|      `/boot/grub2`     | <ul><li>Contains many module files.</li><li>Contains the <code>grub.cfg</code> file, a configuration file which should not be edited manually.</li><li>Contains the <code>grubenv</code> file, a configuration file which should not be edited manually</li></ul>                                                                                                                                                                                                                                     |
|   `/etc/default/grub`  | <ul><li>Contains the entries for creating a default boot entry; typically where customization by administrators will be performed.</li></ul>                                                                                                                                                                                                                                                                                                                                                          |
|      `/etc/grub.d`     | <ul><li>Contains scripts that are run by GRUB 2.</li><li>Scripts can be customized or added to this directory.</li><li><p>Contains the following executable scripts by default:</p><p><code>/etc/grub.d/00_header</code></p><p><code>/etc/grub.d/10_linux</code></p><p><code>/etc/grub.d/20_linux_xen</code></p><p><code>/etc/grub.d/30_os-prober</code></p><p><code>/etc/grub.d/40_custom</code></p><p><code>/etc/grub.d/41_custom</code></p><p><code>/etc/grub.d/90_persistent</code></p></li></ul> |
|     `/usr/lib/grub`    | <ul><li>Stores a couple of library files.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `/sbin/grub2-mkconfig` | <ul><li>Command used to regenerate the <code>/boot/grub2/grub.cfg</code> file.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                              |

The `/etc/default/grub` file can also be modified for the administrator automatically by executing the <mark style="color:red;">`grub-set-default`</mark> command with the menu entry to set as default. For example, to make the second menu entry the default menu entry, an administrator would execute:

```
root@localhost:~# grub-set-default 1
```

If GRUB 2 needs to be installed or reinstalled, then an administrator would execute:

```
root@localhost:# /sbin/grub2-install /dev/sda
```

After installing GRUB 2, the configuration file needs to be generated for the first time. In a Fedore-based distribution, an administrator would execute:

```bash
[root@localhost]# grub2-mkconfig -o /boot/grub2/grub.cfg
```

On Ubuntu-based distribution:

```
root@localhost:~# update-grub
```
