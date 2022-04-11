---
description: >-
  102.1: Design hard disk layout v2 (2)                 104.2: Maintain the
  integrity of file (2)                   104.1: Create partitions and
  filesystems v2 (2)
---

# Module 6: Partitioning

## Chapter 19: Designing a Scheme

<details>

<summary>Key Terms</summary>

**`/ (root) filesystem`** The filesystem that is contained on the same partition.

**`/boot filesystem`** The directory containing everything required for the boot process except for configuration files not needed at boot time.

**`/var`** The directory containing variable data like system logging files, mail and printer spool directories, and transient and temporary files.

**EFI System Partition (ESP)** The Extensible Firmware Interface which is a partition that _**stores the bootloader** for an operating system running **on UEFI systems**._

**MBR** The Master Boot Record is the **first 512 bytes of a storage device**. It contains system bootloader and the storage device's partition table.

**gdisk** An interactive GPT partition table manipulator. It will automatically covert the old-style MBR partition toable to the GPT format.

**Mount** **points**: A connection between a partition's file system and a folder allowing you to access the files on the partition.

**partitions**: The logical division of the available space on a hard drive into one or more sections that can be accessed independently of one another.

</details>

## Understanding Partitioning

Partitioning is necessary in order to optimally use the space that hard drives provide.

There are three steps in the process of making and using partitions:

1. Divide the hard drive into **partitions**.
2. Create and format **filesystems** inside the partitions.
3. **Mount** the filesystem into the directory tree

## Partitioning Naming

In order to distinguish one partition from another, each partition is given a unique name. Recall that everything in Linux is treated as a file, so the **names of devices**, such as **drives and partitions**, are **stored in th **<mark style="color:red;">**`/dev`**</mark>** directory.**

| Device Type                                                                                                                     | Name       | Example                                                  |
| ------------------------------------------------------------------------------------------------------------------------------- | ---------- | -------------------------------------------------------- |
| Drives that start with **`sd`** are either **SATA** (Serial ATA), **SCSI** (Small Computer System Interface) or **USB** drives. | `/dev/sd*` | <p><code>/dev/sda</code></p><p><code>/dev/sdb</code></p> |
| Drives that start with **`hd`** are **PATA** (Parallel ATA), also known as **IDE** (Integrated Drive Electronics) drives.       | `/dev/hd*` | <p><code>/dev/hda</code></p><p><code>/dev/hdb</code></p> |

**Partitions are then given names based on the drive they reside on.** A number is added to the end of the drive name to distinguish one partition from another.

```bash
sysadmin@localhost:/dev$ ls sd*
sda  sda1  sda2  sda3  sda4  sda6  sda7  sda9
```

Similarly, partitions on drive **`hda`** would be `hda1`, `hda2`, etc., while partitions on **`hdb`** would be `hdb1`, `hdb2`, etc.

{% hint style="success" %}
It's critical that you understand that the **files used to refer to hard drives are stored in **<mark style="color:red;">**`/dev`**</mark>** directory** and begin with either `sd` or `hd`
{% endhint %}

## Partition Limitations

### MBR

Historically, **the number of partitions a system can have is limited** <mark style="color:red;"></mark> by the **Master Boot Record (MBR).** Recall that the MBR is usually contained within the first sector or 512 bytes of the disk and contains a bootloader program, as well as the partition table.

Traditional disk using <mark style="background-color:red;">**MBR**</mark> <mark style="background-color:red;"></mark><mark style="background-color:red;"></mark> <mark style="background-color:red;"></mark><mark style="background-color:red;">**partitioning**</mark> <mark style="background-color:red;"></mark><mark style="background-color:red;"></mark> <mark style="background-color:red;"></mark><mark style="background-color:red;">**can have a maximum of 4 primary partitions**</mark>. For example, on a traditional SATA drive, 4 partitions  can be created with device names of `/dev/sdb1` ,`/dev/sdb2`, `/dev/sdb3` and `/dev/sdb4`

For non-Linux OS, multiple partitions are rarely needed. However, on Linux servers, more than 4 partitions are often necessary. As a result, **modern hardware allows the administrator to make 1 of the 4 partitions an **_**extended partition.** _&#x20;

> An extended partition acts as a container for additional partitions called _Logical_ partitions.

![](<../.gitbook/assets/image (13).png>)

The **`/dev/sd4`** is an extended partition. The only thing that are placed in this extended partition are additional partitions, **logical partitions**. In this case `/dev/sd5`, `/dev/sd6`, `/dev/sd7` are logical partitions. Depending upon the type of hard disk and kernel configuration, Linux can access a maximum of either 15 or 63 total partitions when extended partitions are used.

Partitions are typically used to store filesystems. However, for extended partitions, a filesystem is never placed directly on the partition, but rather on each logical partition within the extended partition. In other words, a filesystem is placed on each logical partition.

Up until now, we have only discussed MBR partitioning; however, **some hard drives make use of another partitioning type called the GUID Partition Table (GPT)**

### GPT

Some hard drives make use of another partitioning type called the GUID Partition Table (GPT). A _globally unique identifier_ (GUID) is a unique **128-bit number** used to identify information on a computer system and <mark style="background-color:red;">**it's available on systems that support the Unified Extensible Firmware Interface (UEFI).**</mark>

GPT supports very large disks, up to 9ZB in size. **Extended and logical partitions are not used with GPT**; instead, all partitions are the same, and **GPT supports a maximum of 128 partitions** per disk.

Creating **GPT partitions** is accomplished using a partitioning tool called <mark style="color:red;">**`gdisk`**</mark> ,which is similar to the <mark style="color:red;">**`fdisk`**</mark> utility that is **used for MBR partitions**.

{% hint style="danger" %}
**Systems using UEFI, use the Extensible Firmware Interface(EFI)** specification that was originally released by Intel. The EFI, **also called EFI System Partition (ESP)**, is the partition that **stores the bootloader** for an operating system.
{% endhint %}

## Filesystem

A Filesystem provides the operating system with a way to organize the files and directories. Moreover, a filesystem stores information about files, such as the permissinos of the files, the owner of the files, and the file type.

A filesystem performs the same functions of the Dewey Decimal System for files and directories. Each file will have an entry in the filesystem database, similar to a library catalog, although the database is often referred to as a _table._

{% hint style="info" %}
Keep in mind that there is more than one way to organize books in a library. The same holds true for the filesystems available for Linux; there is more than one type available, and each uses a different technique.
{% endhint %}

Some of the more common filesystem types include:

| Type     | Name                       | Advantages                                                                                                                                              | Disadvantages                                                                                                   |
| -------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **ext2** | Second Extended Filesystem | Works well with small and solid-state disk filesystems                                                                                                  | **No journaling capability**, making it susceptible to data loss in the event of power loss.                    |
| **ext3** | Third Extended Filesystem  | Can be upgraded from existing ext2 filesystem without data loss. This filesystem performs journaling, which **allows for data recovery after a crash.** | Writes more to disk than ext2 because of journaling, making it slower. Does not support very large filesystems. |
| **ext4** | Fourth Extended Filesystem | Support for very large disk volumes and file sizes. **Can operate with or without a journal.** Backwards compatible with ext3 and ext2.                 | Not a huge improvement over ext3. No dynamic inode creation.                                                    |
| **xfs**  | Extents Filesystem         | Works very efficiently with large files. Compatible with the IRIX operating system from SGI. Announced to be the default filesystem for RHEL 7.         | The filesystem cannot be shrunk.                                                                                |
| **vfat** | File Allocation Table      | Supported by almost all operating systems. **Commonly used for removable media**.                                                                       | Unable to support very large disks or files. **Microsoft's intellectual property claims.**                      |
| **iso**  | ISO 9660                   | The International Organization for Standardization standard for optical disc media that is supported by almost all operating systems.                   | Multiple levels and extensions complicate compatibility. Not designed for rewritable media.                     |
| **udf**  | Universal Disc Format      | Designed to replace ISO 9660 and adopted as the standard format for DVDs by the DVD Consortium.                                                         | Write support is limited to support revision 2.01 of the standard.                                              |

{% hint style="danger" %}
A filesystem recovery program, like the <mark style="color:red;">**`fsck`**</mark> command, can often resolve these problems, but this can take quite a bit of time for large filesystems.

**A journaling filesystem enhances filesystem recovery**. For every change made to a file, a journal entry is stored on the hard drive.

In a nutshell, a journaling filesystem **aids in recovering corrupted filesystems** while allowing for **reduced hard drive writes**
{% endhint %}

### Linux Filesystem Components

When talking about Linux the term filesystem often refers to the `ext2/ext3/ext4` family of filesystems. While there are differences between these filesystems, they are similar enough when it comes to core filesystem components.

However, since Linux still operates by the kernel communicating with external resources, all system administrators need to be familiar with the key filesystem components described below:

| Component   | Description                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Superblock  | At the beginning of the filesystem is an area called the superblock. **This area is used to store important information about the filesystem**, including the size of the filesystem, the type of filesystem, and which data blocks (where file data is stored) are available. The superblock is a key component of the filesystem; **a corrupted superblock would make the filesystem inaccessible.** |
| Group Block | The filesystem is divided into smaller sections called groups. **The group block serves to hold data about the grou**p. Each group block also contains a backup copy of the superblock.                                                                                                                                                                                                                |
| Inode Table | Each file is assigned a unique inode number for the filesystem. **This inode number is associated with a table that stores the file's metadata**.                                                                                                                                                                                                                                                      |

### Physical vs. Virtual Filesystems

A physical filesystem is the database structure installed on a partition to organize files and directories.

In Linux there are no drive letters. Instrad, each partition is assigned a device file name as mentioned previously:

| Device Types    | Partition Name | Example                                                    |
| --------------- | -------------- | ---------------------------------------------------------- |
| SATA, SCSI, USB | `/dev/sda*`    | <p><code>/dev/sda1</code></p><p><code>/dev/sda2</code></p> |
| PATA/IDE        | `/dev/hda*`    | <p><code>/dev/hda1</code></p><p><code>/dev/hda2</code></p> |

Users do not access the files that are stored on these partitions by directly using these device file names. Instead, **these device files are merged into the directory structure by **_<mark style="color:red;">**mounting**</mark>** **_** the partition's filesystem under a directory tree.**

![](<../.gitbook/assets/image (8).png>)

From a regular point of view, all of the directories in the previous graphic appear to be from a single hard drive. However, these directories could represent a collection of separate filesystems merged together into a single directory structure, which is called the _**virtual filesystem.**_

![](<../.gitbook/assets/image (12).png>)

As demonstrated from the previous graphic, all of the files under the `/home` directory are really stored in the filesystem that is on the `/dev/sda3` partition. All of the files under the `/var` directory are stored in the filesystem that is on the `/dev/sda2` partition.&#x20;

{% hint style="warning" %}
This makes **`/home`** and **`/var`** mount points directories. The `/` directory is also a mount point directory.
{% endhint %}

**The process of placing a filesystem under a mount point is called **_<mark style="background-color:red;">**mounting**</mark>_**.** This is accomplished either automatically at boot or manually with the <mark style="color:red;">**`mount`**</mark> command.

## Why create Partitions?

To being with, at least one partition needs to be created. Regardless of the operating system, whether Linux, Microsfot Windows, or Mac OSX, at least one single partition needs to be created because **filesystems are stored within partitions, not entire hard disks**.

There are several reasons why an administrator will choose to create multiple partitions when installing a LInux operating system:

* Supporting Multiple Operating Systems - these are called dual boot systems
* Home Directories:
  * Is is easier to backup or restore an entire filesystem than it is to manage each user's home directory.
  * When home directories are separated from the root filesystem, upgrades of the operating system can be performed much more safely.
  * Filesystems can have features enabled on them, such as disk quotas. A disk quota allows the administrator to limit how much space a user uses in a filesystem.
* Common Writable Directories - Separating the **`/tmp`** and **`/var/tmp`** directories allows the administrator to enable disk quotas to limit how much space can be used by each user in those directories.

{% hint style="info" %}
**Consider This**

The **`/tmp`** and **`/var/tmp`** directories **must be world writable for all users,** in order for the operating system to function correctly.
{% endhint %}

* Security - Since each partition has separate inodes and data blocks, corruption to a single partition will be contained to its mount point within the virtual filesystem.
* Heavily Active - swap space. This is a location on the physical drive where data is stored while programs are using it. It  emulates random access memory (RAM) and frees up the actual physical memory for other processing tasks

## Partitioning Layout

There are some requirements in the way partitions will map to the directories that they mount. These requirements are documented in what is called the **FileSystem Hierarchy Standard (FHS)**.&#x20;

| Directory    | Purpose                                                                                                                                                                                                                                       |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/`          | The root filesystem holds the files essential to the operation of the system. It must contain the following directories or symbolic links: `bin`, `boot`, `dev`, `etc`, `lib`, `media`, `mnt`, `opt`, `sbin`, `srv`, `tmp`, `usr`, and `var`. |
| `/boot`      | The `/boot` directory contains the Linux kernel and the boot loader files.                                                                                                                                                                    |
| `/home`      | The `/home` directory is where user home directories are normally created.                                                                                                                                                                    |
| `/tmp`       | The `/tmp` directory is used to create temporary files for the system and users. If this directory is too small, it may prevent applications from functioning correctly.                                                                      |
| `/opt`       | The `/opt` directory is where third-party software is often installed. Some examples include Google Chrome and Google Earth.                                                                                                                  |
| swap         | **Swap is virtual memory that is not mounted on a directory.** This virtual memory is used when the actual memory of the system is low. On systems with large amounts of memory, swap is less important.                                      |
| `/usr`       | **The `/usr` directory contains the bulk of the operating system's files**, including most of the commands and system software.                                                                                                               |
| `/usr/local` | This directory is used for locally installed software that should not be upgraded or updated with the operating system.                                                                                                                       |
| `/var`       | There are many directories that may have heavy activity under `/var` for services like mail, ftp, http, and printing.                                                                                                                         |
| `/boot/efi`  | The `/boot/efi` directory contains the Linux kernel and the boot loader files. It is typically set up automatically by the installer.                                                                                                         |
