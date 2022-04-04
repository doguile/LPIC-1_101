---
description: '101.1: Determine and configure hardware settings v2  Weight: 2'
---

# Module 5: Booting the system

<details>

<summary>Key term</summary>

**`/dev/`** Hardware devices are made available through special files under the `/dev` directory.

**`/proc/`** The process information pseudo-filesystem

**`/sys/`** The mount point for sysfs providing a set of virtual files by exporting information about various kernel subsystems, hardware devices and associated device drivers from the kernel's device model to user space.

**`lsmod`** Prints the contents of the /proc/modules files.

**`lspci`** Prints detailed information about all PCI buses and devices in the system

**`lsusb`** Prints detailed information about all USB buses and devices in the system.

**`modprobe`** Used to _add_ a loadable kernel module (LKM) to the Linux Kernel or to _remove_ an LKM from the kernel.

**`swapspace`** Used when the amount of physical memory (RAM) is full. If the system needs more memory resources and the RAM is full, inactive pages in memory are moved to the swap space.

</details>

## Chapter 15: Hardware Configuration

## Core Hardware

Components that are essential to operate a computer are considered _core hardware._ Every system should include a central processing unit, random access memory, and some kind of firmware.&#x20;

Other hardware that is essential for a complete system includes some type of persistent storage device like a mechanical hard drive or solid-state drive (SSD), a method of controlling the device, either remotely, or by using a display device/monitor, a keyboard, and a mouse. Most systems also include one or more networking devices, possibly a sound device, and ports for connecting external devices.

### Central Processing Unit

The Central Processing Unit (CPU) is the brain of the computer, where the instructions to perform calculations or manipulate data are processed. There are numerous types of CPUs that are able to work with Linux, but the most common is the **64-bit x86\_64 type**, and decreasingly, the **32-bit x86** type.

A prime location for CPU information is the firmware settings. Viewing the contents of the <mark style="color:red;">`/proc/cpuinfo`</mark> file will display detailed information, including the model name, speed in MHz, and specific features available in a list of flags.

The <mark style="color:red;">`uname`</mark> command is a valuable tool for quickly determining system information from the command line.

```
sysadmin@localhost:~$ uname
Linux
```

The <mark style="color:red;">**`uname`**</mark> command has access to the files `/proc` and `/sys` directories which can **display a lot of organized information about your system.** Running <mark style="color:red;">**`uname`**</mark> with the <mark style="color:red;">**`-a`**</mark> option will show the following information:

| Information       | Option | Example                                               |
| ----------------- | ------ | ----------------------------------------------------- |
| Kernel name       | `-s`   | `Linux`                                               |
| Nodename          | `-n`   | `localhost`                                           |
| Kernel release    | `-r`   | `4.4.0-72-generic`                                    |
| Kernel version    | `-v`   | `#93~14.04.1-Ubuntu SMP Fri Mar 31 15:05:15 UTC 2017` |
| Machine hardware  | `-m`   | `x86_64`                                              |
| Processor         | `-p`   | `x86_64`                                              |
| Hardware platform | `-i`   | `x86_64`                                              |
| Operating System  | `-o`   | `GNU/Linux`                                           |

```bash
sysadmin@localhost:~$ uname -a
Linux localhost 4.4.0-72-generic #93~14.04.1-Ubuntu SMP Fri Mar 31 15:05:15 UTC
2017 x86_64 x86_64 x86_64 GNU/Linux
```

**Another way to get more detailed information** than the <mark style="color:red;">`uname`</mark> command can give you is to run the <mark style="color:red;">**`lscpu`**</mark> **command** which is very useful in determining the hardware's capabilities.

```bash
sysadmin@localhost:~$ lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    1
Core(s) per socket:    4
Socket(s):             2
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 44
Stepping:              2
```

## Random Access Memory

The Random Access Memory (RAM) of a system is used to **temporarily store data and instructions for the operating system** and the programs that are executing.

The maximum amount of memory that can be used with a **32-bit processor is 4GiB**, whereas a 64-bit processor can theoretically use 16EiB of memory.

From a practical perspective, if a system doesn't currently have at least 1GiB of RAM, then it may not be able to use the Graphical Unit Interface (GUI) with Linux.&#x20;

### Swap Memory

If a system **doesn't have sufficient RAM for the processes** you are executing on it, then it **will use virtual memory, called **<mark style="color:red;">**swap space**</mark> in Linux.

{% hint style="info" %}
**Swap space** is hard drive space that is temporarily **used to hold data that exceeds the amount of RAM available.**
{% endhint %}

When the system begins to run low on memory, it will "_swap out_" data that is least in demand at the moment, so that RAM space can free up for something that is currently in demand.&#x20;

Swap space comes in two types: a **swapfile** that would reside on an existing filesystem, or a **swap partition**, which is a section of a disk that is dedicated to swap and is formatted with the swap filesystem.

A <mark style="color:red;">**swap partition**</mark> is an essential part of a properly configured Linux system and **yields the best performance for swap operations**. <mark style="color:red;">**Swapfiles**</mark> have the overhead of being on an existing filesystem and should be **used sparingly, and only if absolutely necessary**.

The amount of RAM in a system **can be viewed in the firmware settings**. For a very detailed breakdown of how much memory a system has and how it is being used, view the <mark style="color:red;">**`/proc/meminfo`**</mark> file:

```
sysadmin@localhost:~$ cat /proc/meminfo
MemTotal:       132014640 kB
MemFree:        67439868 kB
MemAvailable:   99487364 kB
Buffers:         2116936 kB
Cached:         27429740 kB
SwapCached:           40 kB
Active:         14409408 kB
```

For a quick summary of RAM memory and swap space, execute the <mark style="color:red;">**`free`**</mark> command:

```bash
sysadmin@localhost:~$ free
              total        used        free      shared  buff/cache   available
Mem:         128920       35120       89205           2        4594       93190
Swap:        131050         140      130910
```

## Firmware

**Firmware is software that has been written to non-volatile memory such as read-only memory (ROM) or flash memory.** There are several types of firmware that may be present in a computer system. On each device that provides services to a system, there is typically a ROM chip that contains firmware for the device.

The motherboard firmware contains the code that allows the integrated components of the system to work together. This firmware test the components upon startup, identifies and initializes these components, and attempts to find a _bootloader_ to load an operating system.

Originally, this firmware **was know as the** **Basic Input/Output System (BIOS)**, system ROM, or ROM BIOS. BIOS is used to provide basic services, called input and output services before an operating system is loaded.

Recently, computer manufacturers have begun to **replace the traditional BIOS with** something called the **Unified Extensible Firmware Interface (UEFI)**; however, the functions of UEFI appear so similar to BIOS that many people still refer to the system firmware as BIOS.

If a system has **UEFI** firmware, then it may be **more challenging to boot the Linux operating system** due to a feature called <mark style="color:red;">**Secure Boot**</mark><mark style="color:red;">.</mark>&#x20;

> If Secure Boot is enabled, then the bootloader must be cryptographically signed by a digital key that is recognized by the firmware. If the bootloader is not properly signed, then booting may still be possible by disabling Secure Boot in the firmware settings, in favor of the **Compatibility Support Module (CSM)**.

{% hint style="info" %}
The firmware settings can even be used to affect the use of external devices like keyboards.
{% endhint %}

As advancements in integration of peripherals have ocurred, more components have been placed on-board, meaning they are **embedded into the motherboard** of a system. These integrated peripherals can be managed through firmware as well.

Commonly, server system may be configured to run headless, that is without keyboard, mouse or monitor. Typically, **the BIOS will prevent a computer from booting without a keyboard present**.  It may be necessary to change the firmware settings to disable this feature for headless systems.

## Mass Storage Devices

Although Linux doesn't strictly require a mass storage device, most systems will include one or more of such devices. The most common mass storage device is the mechanical hard disk (or fixed disk). These type of disks come with a variety of interfaces or ways that they connect to the computer system.

There are quite a few mass storage interfaces that are still in use today:

* The **Small Computer System Interface (SCSI)** is one of the oldest and requires SCSI controller in the system to control one or more disk drives that connect to it.
* The **Integrated Drive Electronics (IDE)** or **Parallel Advanced Technology Attachment (PATA)** type interface includes the controller directly on each drive.
* The most common interface used for internal mass storage devices today is the **Serial Advanced Technology Attachment (SATA)** type. Each SATA drive is connected directly to the system board by a cable.
* For external drives, the **Universal Serial Bus (USB)** interface is the most common, but there are others standards such as FireWire and Thunderbolt

{% hint style="info" %}
**Note**

Data storage devices use either serial or parallel interfaces. The **S** in SC**S**I, in **S**ATA, and in U**S**B all stand for the word serial. That is why they appear as `/dev/sda1` meaning **Dev**ice **S**erial **D**rive **A** partition **1**.
{% endhint %}

## Plug and Play

In addition to the core hardware, there are many other components, commonly known as peripherals, that may be used with the computer system. It is important to know if a peripheral requires that the power to the computer system be turned off at the time it is connected, or if the peripheral may be connected while the computer system is on.

Devices that are supposed to be **connected when the power is off** are known as <mark style="color:red;">**coldplug devices**</mark>**.** Devices that can be connected **when the power is on** are known as <mark style="color:red;">**hotplug devices**</mark>.

{% hint style="info" %}
**Consider This**

Typically, USB devices are hot pluggable. This means that the USB device will be auto-recognized by the running operating system, so you don't have to do anything for the system to “see” the device; just plug it in, and the USB drivers will see it, auto-configure, and connect it properly.
{% endhint %}

## Hardware Resources

In order for a device to work correctly, certain resources must be allocated to it.

**There are four types of hardware resources that devices use to communicate with the system**. As some of these resources refer to input and output, part of their name may be abbreviated as IO. The four resources are: <mark style="color:red;">**IO ports**</mark><mark style="color:red;">,</mark> <mark style="color:red;"></mark><mark style="color:red;">**IO memory**</mark><mark style="color:red;">,</mark> <mark style="color:red;"></mark><mark style="color:red;">**interrupt requests(IRQ)**</mark>, and <mark style="color:red;">**Direct Memory Access (DMA)**</mark> channels:

* <mark style="color:orange;">**IO ports:**</mark> Memory addresses that _**allow for communication with hardware devices**_. The current system addresses in use can be viewed by executing the following command:

```bash
sysadmin@localhost:~$ cat /proc/ioports
0000-0cf7 : PCI Bus 0000:00
  0000-001f : dma1
  0020-0021 : pic1
  0040-0043 : timer0
  0050-0053 : timer1
  0060-0060 : keyboard
  0064-0064 : keyboard
```

* <mark style="color:orange;">**IO Memory**</mark><mark style="color:orange;">:</mark> A section or location that **acts much like a RAM** that is presented to the processor via the system bus. These are **used to pass and store data for access to devices on the system.**

```bash
sysadmin@localhost:~$ cat /proc/iomem
00010000-0009ffff : System RAM
000a0000-000bffff : PCI Bus 0000:00
000c0000-000c7fff : Video ROM
000c8000-000cdfff : Adapter ROM
000f0000-000fffff : System ROM
00100000-be777fff : System RAM
06000000-0680bdb2 : Kernel code
0680bdb3-06f45abf : Kernel data
```

* <mark style="color:orange;">**Interrupt Requests (IRQ)**</mark> - An interrupt is a hardware signal that _**pauses or stops a running program so that the interrupt handler can switch to running another program**_, or send and receive data. The <mark style="color:red;">**`/proc/irq`**</mark> directory contains configuration information for each IRQ on the system.
* <mark style="color:orange;">**Direct Memory Access (DMA):**</mark> - A **method by which particular hardware items in the system can directly access `RAM`, without going through the `CPU`**. This speed up access, as the CPU would otherwise be fully tasked during such access.

```
sysadmin@localhost:~$ cat /proc/dma
 4: cascade
```

{% hint style="danger" %}
Note that, **with the exception of interrupt requests**, <mark style="color:red;">**these resources**</mark> <mark style="color:red;">**cannot be shared between devices**</mark>
{% endhint %}

## Viewing Hardware

Viewing the details of what hardware is attached or contained in a running Linux system is important to successfully troubleshoot problems that may occur during boot up and day-to-day operations.

{% hint style="info" %}
**Consider this**

Modern computers typically use the **Peripheral Component Interconnect Express (PCIe) bus to **<mark style="color:red;">**connect components inside the computer**</mark>. For example video,sound, network, etc.
{% endhint %}

**A `BUS` is not only used to refer to actual physical connections, but also software components designed to connect programs and certain communication protocols.**&#x20;

Buses can be alternatively **grouped into **_**internal**_** and **_**external**_** bus types**. **Internal** **buses** are considered to be inside the actual computer, while **external buses** are used to attach external devices to the computer.

> Good examples of internal buses are the PCIe bus, the older **Industry Standard Architecture (ISA)** bus, and the very popular **Small Computer Systems Interface (SCSI)** bus. An example of an external bus type would be the **Universal Serial Bus (USB)**

### `lspci`

First among these is the <mark style="color:red;">**`lspci`**</mark> command, designed to **show the user the PCI buses and devices attached** to them.

In the output of the command below you will see the various bridges listed; those are connections between the different buses on the system, interconnecting them with each other.

Additionaly users can view the USB controller, ethernet, sound and the other video peripheral connection buses and controllers.

```bash
sysadmin@localhost:~$ lspci
00:00.0 Host bridge: Intel Corporation 5520 I/O Hub to ESI Port (rev 13)
00:01.0 PCI bridge: Intel Corporation 5520/5500/X58 I/O Hub PCI Express Root Por
t 1 (rev 13)
00:03.0 PCI bridge: Intel Corporation 5520/5500/X58 I/O Hub PCI Express Root Por
t 3 (rev 13)
```

Finally, if a user has an issue with a component and is able to see that component mentioned in the <mark style="color:red;">**`lspci -v`**</mark> output, they can **get more information** about the malfunctioning component by **referring to the multiple digit vendor and device code.**&#x20;

For example, to get more information about the IDE interface from the machine above, the user can run the <mark style="color:red;">**`lspci`**</mark> command with the <mark style="color:red;">**`-v`**</mark> and <mark style="color:red;">**`-s`**</mark> options followed by the vendor and device code as parameter.&#x20;

{% hint style="warning" %}
The <mark style="color:red;">**`-s`**</mark> option will **allow for a domain to be specified** which will display information solely about devices in that domain.
{% endhint %}

```bash
sysadmin@localhost:~$ lspci -v -s 00:07.0
00:07.0 PCI bridge: Intel Corporation 5520/5500/X58 I/O Hub PCI Express
Root Port 7 (rev 13) (prog-if 00 [Normal decode])
        Flags: bus master, fast devsel, latency 0, IRQ 30
        Bus: primary=00, secondary=06, subordinate=06, sec-latency=0
        Capabilities: <access denied>
        Kernel driver in use: pcieport
lspci: Unable to load libkmod resources: error -12lsusb
```

### `lsusb`

For **viewing the external devices**, the <mark style="color:red;">**`lsusb`**</mark> command will show those that are specifically connected to the Universal Serial Bus (USB)

```bash
sysadmin@localhost:~$ lsusb
Bus 002 Device 006: ID 0624:0249 Avocent Corp. Virtual Keyboard/Mouse
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 006 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 005 Device 003: ID 0624:0248 Avocent Corp. Virtual Hub
```

**Another tool for viewing details about USB devices connected** to the system is the <mark style="color:red;">**`usb-devices`**</mark> command. This script, which when executed will display information about the USB device that can otherwise be found in the `/sys` or `/proc` directories, including the USB device number, vendor, port and more:

```bash
sysadmin@localhost:~$ usb-devices | tail -n 15
S:  Manufacturer=Avocent
S:  Product=USB Composite Device-0
S:  SerialNumber=20120430
C:  #Ifs= 2 Cfg#= 1 Atr=c0 MxPwr=2mA
I:  If#= 0 Alt= 0 #EPs= 1 Cls=03(HID  ) Sub=01 Prot=01 Driver=usbhid
I:  If#= 1 Alt= 0 #EPs= 1 Cls=03(HID  ) Sub=01 Prot=02 Driver=usbhid
```

{% hint style="info" %}
If the user requires more information than the <mark style="color:red;">`lspci`</mark> and <mark style="color:red;">`lsusb`</mark> commands show normally, simply append a <mark style="color:red;">`-v`</mark> option to either command and a great deal more information will be show.
{% endhint %}

To isolate the details of a specific USB device, find the vendor and device code in the output of the <mark style="color:red;">**`lsusb`**</mark> command, then use the <mark style="color:red;">**`lsusb`**</mark> command again, this time with the <mark style="color:red;">**`-v`**</mark> and <mark style="color:red;">**`-d`**</mark> options to get isolated details of a device.

The <mark style="color:red;">**`-d`**</mark> option **will allow for a vendor or product ID to be specified**, which will only display devices with that number.

```bash
sysadmin@localhost:~$ lsusb -v -d 0624:0248 | less
Bus 005 Device 003: ID 0624:0248 Avocent Corp. Virtual Hub
Device Descriptor:
  bLength                18
  bDescriptorType         1
  bcdUSB               1.10
  bDeviceClass            0 (Defined at Interface level)
  bDeviceSubClass         0
  bDeviceProtocol         0
  bMaxPacketSize0        64
```

## Hardware Subsystems

Devices built into the computer system are typically not removable unless the system is shut down and power disconnected for safety

The concept of hotplugging works extremely well in modern Linux distributions due to a trio of device managment tools.

### `udev`

The trio of device managment tools starts with _<mark style="color:red;">**`udev`**</mark>_, a device managment subsystem that manages the <mark style="color:red;">`/dev/`</mark> directory and will automatically **create and destroy node points** (references to a device) **for devices that are attached to the system**, or subsequently removed.

To accomplish this, the <mark style="color:red;">**`udev`**</mark> subsystem maintains a pseudo-filesysytem mounted as the `/dev` directory. **The files in the **<mark style="color:red;">**`/dev`**</mark>** directory represents devices currently connected to the system**.&#x20;

When the Linux kernel  detects a device being connected, <mark style="color:red;"></mark> **the **_<mark style="color:red;">**udev**</mark>_** daemon is used to create a device file (or node) in the **<mark style="color:red;">**`/dev`**</mark>** directory**<mark style="color:red;">.</mark> If the device is removed, the udev daemon then removes the device node in the `/dev` directory.

{% hint style="info" %}
Configuration files in the `/etc/udev/rules.d` directory are used to define rules that assign specific ownerships, permissions, and persistent names to these devices.

These files allow a user to configure how udev handles the device it manages.
{% endhint %}

### `sysfs`

Another in-memory filesystem that consists of directories and text files that **contain values about the kernel's operation and configuration**

The <mark style="color:red;">**`sysfs`**</mark> subsystem is tipically mounted as the **`/sys`** subdirectory. The **`/sys`** directory and sysfs exist because there is a need to provide information about the kernel, its attribute, and the contents to users via programs such as <mark style="color:red;">`ps`</mark> , <mark style="color:red;">`top`</mark> ,and another programs that provide information to the regular user through command line output.

Modern systems use <mark style="color:red;">`sysfs`</mark> to express kernel information into the **`/sys`** directory because the _procfs_ subsystem (and the `/proc` directory) had become increasingly busy and cluttered. &#x20;

### Hardware Abstraction Layer (HAL) aka. `hald`

The last of the trinity of device management tools is the <mark style="color:red;">**`hald`**</mark> .As the kernel detects a device, it puts the information about the device into the appropiate files in the **`/sys`** directory.&#x20;

The <mark style="color:red;">`halds`</mark> is responsible for **discovering and maintaining a list of connected devices and their attributes** by monitoring the files in the **`/sys`** directory

To view the list of devices and their attributes that have been stored by hald, execute the <mark style="color:red;">**`lshal`**</mark> command.&#x20;

```bash
sysadmin@localhost:~$ lshal | grep cdrom | grep true
 storage.cdrom.dvd = true   (bool)
 storage.cdrom.mrw = true   (bool)
 storage.cdrom.mrw_w = true   (bool)
 storage.cdrom.support_media_changed = true   (bool)
 storage.cdrom.support_multisession = true   (bool)
```

Finally, when programs want information about devices, they are able to query hald by using **D-Bus.**

**D-Bus** is a software bus that allows individual and groups of processes to communicate on a single virtual bus or channel, a feature called **Interprocess Communication (IPC).**

![](<../.gitbook/assets/image (4) (1).png>)

### D-Bus

Programs can also register themselves with D-Bus to receive notifications from hald when specific types of hardware events occur. When the state of a hardware device changes, **`hald`** uses D-Bus to send notifications to those programs that have been registered for that type of hardware event.

System typically uses <mark style="color:red;">**`udev`**</mark> for its device management task. **The job of **<mark style="color:red;">**`udev`**</mark>** is to let your computer know of devices events, among other tasks.**&#x20;

{% hint style="info" %}
**`Udev`** can manage any device that shows a link in the `/dev` directory when attached to the system, which `udev` is able to do through scripts know as _udev rules._

_A udev rule_ is something that perform an action when a device is inserted, such as a thumb drive.
{% endhint %}

> Udev can detect when a device has been attached or removed. Udev rules can establish custom actions that are taken when these events occur.

You can use the <mark style="color:red;">**`udevadm`**</mark> command to view the pertinent information that would allow you to directly specify a device within a udev rule when it's attached, and then execute specific actions on that device.

There are two ways to do this:

* When a device is inserted
* When you want to query a device that is either already attached or is built-in to the system, such as the main storage device (the `/dev/sda` device for example.)

To **watch what happens when a device is inserted or attached**, run the <mark style="color:red;">**`udevadm monitor`**</mark> command:

```bash
sysadmin@localhost:~$ udevadm monitor
monitor will print the received events for:
UDEV: the event udev sends out  after rule processing
KERNEL - the kernel uevent
```

To query an already-attached device for the necessary information, execute the command below:

```bash
sysadmin@localhost:~$ udevadm info /dev/sda
P: /devices/pci0000:00/0000:00:07.1/ata1/host0/target0:0:0/0:0:0:0/block/sda
N: sda
S: disk/by-id/ata-VMware_Virtual_IDE_Hard_Drive_00000000000000000001
S: disk/by-id/wwn-0x5000c2947bd39bdb
S: disk/by-path/pci-0000:00:07.1-ata-1
E: DEVLINKS=/dev/disk/by-id/wwn-0x5000c2947bd39bdb /dev/disk/by-path/pci-0000:00:07.1-ata-1 /dev/disk/by-id/ata-VMware_Virtual_IDE_Hard_Drive_00000000000000000001
E: DEVNAME=/dev/sda
E: DEVPATH=/devices/pci0000:00/0000:00:07.1/ata1/host0/target0:0:0/0:0:0:0/block/sda
E: DEVTYPE=disk
```

## Kernel Modules

Kernel modules can be used for more than supporting devices; since modules are simply software that is able to run within the kernel, they are able to be used for virtually anything.

Some common uses besides device drivers include filesystems modules, networking protocols modules, and cryptographic algorithms modules.&#x20;

To view the **list of loaded kernel modules**, use the <mark style="color:red;">**`lsmod`**</mark> command

```bash
sysadmin@localhost:~$ lsmod
Module                  Size  Used by
af_packet_diag         16384  0
netlink_diag           16384  0
dccp_diag              16384  0
dccp                   73728  1 dccp_diag
```

> The "Used by" indicates how many "things" depend on the current module being loaded. These "things" could include other modules, processes, or other features.
>
> And "by" shows the name of the module that depends on the current module

Additionally, you can get **more details about a module** by using the <mark style="color:red;">**`modinfo`**</mark> command. For example, to learn more about the `snd` module, execute the following command:

```bash
sysadmin@localhost:~$ modinfo snd
filename:   	/lib/modules/3.13.0-35-generic/kernel/sound/core/snd.ko
alias:      	char-major-116-*
license:    	GPL
description:	Advanced Linux Sound Architecture driver for soundcards.
author:     	Jaroslav Kysela <perex@perex.cz>
license:    	GPL
description:	Jack detection support for ALSA
author:     	Mark Brown <broonie@opensource.wolfsonmicro.com>
srcversion: 	0EBCEABF53FC967306ED228
depends:    	soundcore
```

To get a list of all available modules, use the <mark style="color:red;">**`modprobe -l`**</mark> command:

```bash
sysadmin@localhost:~$ modprobe -l | head
kernel/arch/x86/kernel/cpu/mcheck/mce-inject.ko
kernel/arch/x86/kernel/cpu/cpufreq/powernow-k8.ko
kernel/arch/x86/kernel/cpu/cpufreq/mperf.ko
kernel/arch/x86/kernel/cpu/cpufreq/acpi-cpufreq.ko
kernel/arch/x86/kernel/cpu/cpufreq/pcc-cpufreq.ko
```

Normally, kernel modules are loaded automatically by the kernel. **To load a module manually execute the **<mark style="color:red;">**`modprobe`**</mark>** command with the name of the module.**

{% hint style="danger" %}
Used to add a loadable kernel module (LKM) to the Linux kernel or to remove an LKM from the kernel
{% endhint %}

One nice feature of the <mark style="color:red;">**`modprobe`**</mark> command is that it will **automatically load all dependency modules**, so in the case of loading the `ext4` module, it would automatically load the `crc16`, `mbcache`, and `jdb2` modules.

**The **<mark style="color:red;">**`modprobe`**</mark>** command can also be used to remove modules from memory with the **<mark style="color:red;">**`-r`**</mark>** option**. Executing the <mark style="color:red;">`modprobe -r ext4`</mark> command would remove the `ext4` module from memory, and it will automatically remove the `crc16`, `mbcache`, and `jdb2` modules from memory as long there was nothing else that depended upon them:
