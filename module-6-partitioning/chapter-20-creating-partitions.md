---
description: >-
  102.1: Design hard disk layout v2  Weight:
  2                                                                                     
  104.1: Create partitions and filesystems v2 Weight: 2
---

# Chapter 20: Creating Partitions

<details>

<summary>Key terms</summary>

**`fdisk`** Command used to manipulate the MBR partition table for Linux. This utility can be used to create, modify, and delete partitions for a fixed disk.

**`gdisk`** An interactive GPT partition table manipulator. It will automatically covert the old-style MBR partition toable to the GPT format.

**`mke2fs`** Command used to create an ext2/ext3/ext4 filesystem.

**`mkfs`** Command used to build a Linux file system on device.

**`mkswap`** Command used to build a Linux swap area. A device argument will be need to specify where the swap area will be created.

**`parted`** A diks partitioning and partition resizing utility. It allows the administrator to destroy, create, resize, move and copy ext2, linux-swap, FAT and FAT32

**`swap space`** Used when the amount of physical memory (RAM) is full.

</details>

## Creating Partitions After Installation

Originally, hard disk were called fixed disks because they were not removable. The most command line tool for editing the partition tables on disks is called <mark style="color:red;">**`fdisk`**</mark>. **This command can be used to create, modify and list the partitions on a hard drive.**

One of the benefits of the <mark style="color:red;">**`fdisk`**</mark> tool is that it is very forgiving; if a mistake is made while using the tool, simply quit the program.

The <mark style="color:red;">**`fdisk`**</mark> program can be used in two ways: ** **_**interactive**_** and **_**non-interactive**._ The **interactive** mode is used to <mark style="background-color:red;">modify the partitions</mark>, and the **non-interactive** mode is used to <mark style="background-color:red;">list partitions</mark>.

> In either mode, the <mark style="color:red;">`fdisk`</mark> program requires root privileges to run.

The units option <mark style="color:red;">`-u`</mark> will list the starting and ending locations for each partition in sectors instead of cylinders.

{% hint style="info" %}
On almost every Linux distributions, the default <mark style="color:red;">`fdisk`</mark> output is displayed in cylinders. One exception is **Ubuntu, which displays output in sectors** by default. To display the output in cylinders, use the <mark style="color:red;">`-u=cylinders`</mark> option.
{% endhint %}

The <mark style="color:red;">`-c`</mark> option disables the warning regarding compatibility issues with the MS-DOS operating system.

### Displaying Partitions

To use <mark style="color:red;">**`fdisk`**</mark> in its **non-interactive mode**, add the <mark style="color:red;">**`-l`**</mark> option. To demonstrate.

```bash
root:[~]# fdisk -lc /dev/sda
Disco /dev/sda: 465,78 GiB, 500107862016 bytes, 976773168 sectores
Disk model: TOSHIBA
Unidades: sectores de 1 * 512 = 512 bytes
Tamaño de sector (lógico/físico): 512 bytes / 4096 bytes
Tamaño de E/S (mínimo/óptimo): 4096 bytes / 4096 bytes
Tipo de etiqueta de disco: gpt
Identificador del disco: XXXX-XXX-XXXX-XXX-

Dispositivo Comienzo     Final  Sectores Tamaño Tipo
/dev/sda1       2048   1050623   1048576   512M Sistema EFI
/dev/sda2    1050624   2549759   1499136   732M Sistema de ficheros de Linux
```

### `fdisk` Interactive Mode

In the interactive mode, a system administrator can use the <mark style="color:red;">**`fdisk`**</mark> command to **create and modify partitions**. To enter the interactive mode, **do not use the **<mark style="color:red;">**`-l`**</mark> option, **but still use **<mark style="color:red;">**`-c`**</mark>** and **<mark style="color:red;">**`-u`**</mark>** option**. **The pathname for the disk to edit is required**. For example, to edit the partitions for the first SATA `/dev/sda` hard drive, execute the following command to display a prompt

```
root@localhost:~# fdisk -cu /dev/sda
Command (m for help):
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)
```

Before creating any partitions, it is a good idea to **print the partition table** by choosing the <mark style="color:red;">**`p`**</mark> **command** action.

### Creating Partitions

In order to create a **new partition, the **<mark style="color:red;">**`n`**</mark>** command** action should be chosen:

```bash
Command (m for help): n
```

This command will prompt the system administrator to answer several questions as described below.

#### Type of partition

```
Command Action
   e   extended
   p   primary partition (1-4)
```

The choices that are available to answer this question will vary, depending on what partitions already exist:

* If no extended partition has been created, the choices will be <mark style="color:red;">**`e`**</mark> for an extended partition or <mark style="color:red;">**`p`**</mark> for a primary partition.
* If the extended partition has ben created, the <mark style="color:red;">**`fdisk`**</mark> utility will automatically create a logical partition within the free space of the extended partition.

{% hint style="info" %}
Recall that there **can be only 4 primary partitions** or three primary partitions with one extended.
{% endhint %}

**Delete partitions by using the **<mark style="color:red;">**`d`**</mark>** command**. Be careful to avoid deleting necessary existing partitions, as this may result in an unsuable system

### Changing the Filesystem Type

By default, the <mark style="color:red;">**`fdisk`**</mark> utility **sets the filesystem type to **<mark style="color:red;">**`Id 83 (Linux)`**</mark> for primary and logical partitions. **For extended partitions, the `Id` should be `5` ** and should never be changed.

To change the filesystem type, use the <mark style="color:red;">**`t`**</mark> command.

### Saving Changes

If the changes that have been made to the in-memory partition table are correct, **commit changes to disk with **<mark style="color:red;">**`w`**</mark> followed by ENTER. The <mark style="color:red;">`fdisk`</mark> utility will write the in-memory changes to the actual MBR and exit. However, it is also possible to quit the <mark style="color:red;">`fdisk`</mark> utility without making any changes to the disk by using the <mark style="color:red;">**`q`**</mark> command.

Note the following possible error after saving changes:

```
The partition table has been altered!

Calling iotctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The table will be used at the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

As mentioned in the output of this error, the <mark style="color:red;">`partprobe`</mark> or <mark style="color:red;">`kpartx`</mark> command can be executed to fix this issue if these commands are installed on the system. If not, then the system will need to be rebooted before the new partitions can be used.

### The `sfdisk` Command

The <mark style="color:red;">**`sfdisk`**</mark> command can be **used to automate partitioning**, it is also **capable of backing up** and **restoring the current partition table**.

To back up the partition table, first determine the names of the disk devices. The <mark style="color:red;">**`sfdisk`**</mark> command will list the disk(s) and their sizes when provided the <mark style="color:red;">**`-s`**</mark> option.

```bash
root@localhost:~: sfdisk -s
/dev/sda:   500088608
```

Before partitioning the disk, it would be a good idea to back up the current partition table data by using the <mark style="color:red;">**`-d`**</mark> option to the <mark style="color:red;">**`sfdisk`**</mark> command:

```
root@localhost:~# sfdisk -d /dev/sda > sda.disk
```

In the event that a mistake is made while using partition editing tools, the partition table can be restored to the original partition table by executing the <mark style="color:red;">**`sfdisk`**</mark> command with the <mark style="color:red;">**`-f`**</mark> option

```
root@localhost:~# sfdisk -f /dev/sda < sda.disk
```

## Managing GPT

Some hard drives make use of a partitioning technology called **Master Boot Record (MBR)** while others make use of a **partitioning** **type** called **GUID Partitioning Table (GPT)**

**The GPT disks use a newer type of partitioning**, which allows the user to divide the disk into more partitions than what MBR supports. GPT also allows having partitions, which can be larger than 2TB (MBR does not).

The tools for managing GPT disks are named similarly to the <mark style="color:red;">`fdisk`</mark> counterparts: the <mark style="color:red;">**`gdisk`**</mark> ,<mark style="color:red;">**`cgdisk`**</mark> and <mark style="color:red;">**`sgdisk`**</mark> programs.

To **create and manage GPT partitions** from the CLI, you can **use the **<mark style="color:red;">**`gdisk`**</mark> utility, **also called **<mark style="color:red;">**GPT**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**`fdisk`**</mark>. It operates in a similar fashion to **`fdisk`** except it operates on GPT partitions and requires the device to be specified in order to work.

![](<../.gitbook/assets/image (19).png>)

When you speciy a blank disk, it will scan the device and report back no partition information. Typing a question mark `?` character returns a list of command option available, **type `n` to add a new partition.**

The <mark style="color:red;">**`v`**</mark> **command** will verify the partition to ensure it is **free from errors**.

```bash
Command (? for help): v

No problems found. 2014 free sectors (1007.0 KiB) available in 1
segments, the largest of which is 2014 (1007.0 KiB) in size.
```

The <mark style="color:red;">**`o`**</mark>** command allows you to create a new empty partitition**; it verifies that you want to delete existing partitions before proceeding

```bash
Command (? for help): o
This option deletes all partitions and creates a new protective MBR.
Proceed? (Y/N): y
```

## GNU Parted

Another available tool for **creating and resizing partitions** on a hard drive is the GNU Parted program. The GNU Parted program includes the <mark style="color:red;">**`parted`**</mark> command line tool and the <mark style="color:red;">**`gparted`**</mark> graphical interface tool.&#x20;

{% hint style="info" %}
One benefit of using GNU Parted is that unlike the **`gdisk`** and **`fdisk`** tools, which are destructive partitioners, **GNU Parted will non-destructively resize a partition as well as the filesystem on top of it.**
{% endhint %}

The <mark style="color:red;">**`parted`**</mark>program can be used in two ways: command line mode and interactive mode.&#x20;

```
parted DEVICE
```

To use <mark style="color:red;">**`parted`**</mark> in command line mode, **the `DEVICE` argument must be followed by options to create or modify a partition**. To see a list of option available for <mark style="color:red;">**`parted`**</mark>, use the `--help` option.

{% hint style="warning" %}
To view the disk on the system, use the <mark style="color:red;">**`lsblk`**</mark> command.
{% endhint %}

```bash
root@localhost:~# lsblk
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda    8:0    0 3.7T  0 disk
|-sda1 8:1    0 512M  0 part
|-sda2 8:2    0 3.5T  0 part /etc/hosts
`-sda3 8:3    0 128G  0 part [SWAP]
sdb   8:16    0 20G  0  disk
```

To view any existing partition information with the <mark style="color:red;">**`parted`**</mark> command, use the following command:

```bash
root@localhost:~# parted /dev/sdb print
Model: ATA VMware Virtual I (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table:
Disk Flags:

Number  Start  End  Size  Type  File system  Flags
```

There is no partition information listed. To make the disk partitionable, a disklabel must be created. This is done with the following command:

```
parted /dev/sdb mklabel msdos
```

Now that a partition table is written to the disk, partitions can be created on the disk. To create a primary partition that takes up the first 50% of the disk, use the following

```
parted /dev/sdb mkpart primary 0% 50%
```

```
root@localhost:~# parted /dev/sdb print
Model: ATA VMware Virtual I (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table:
Disk Flags:

Number  Start    End      Size    Type     File system  Flags
1       1049kB   10.7GB   10.7GB  primary
```

## Logical Volume Management

Logical Volume Management is a method of managing hard disk space that **provides more flexibility** than traditional partitioning disks.

One of the great benefits of LVM is the ability to increase the size of the partition , referred to as a volume in LVM, as needed without the necessity for system downtime.

### LVM Concepts

The steps to implement LVM include the following:

1. Connect the physical devices to the system
2. Use <mark style="color:red;">`pvcreate`</mark> to convert the desired devices into physical volumes, which will allocate them for inclusion in the LVM scheme.
3. Use <mark style="color:red;">`vgcreate`</mark> to incorporate all of the desired physical volumes into a virtual collection called a volume group.
4. Use <mark style="color:red;">`lvcreate`</mark> to create the LVM version of disk partitions in the volume group created previously.

The main advantages of using LVM in general and logical volumes, in particular, is the ability to group together space from multiple physical devices, resizes them, and much more.

To develop a better understanding of how these steps work, consider a situation in which three new hard drives are added to the system: `/dev/sdb`, `/dev/sdc`, and `/dev/sdd`.

At this point, they are just three hard drives that don't have anything on them, including a partition table. To use these as part of LVM, first execute the <mark style="color:red;">`pvcreate`</mark> command on each one:

```bash
root@localhost:~# pvcreate /dev/sdb
root@localhost:~# pvcreate /dev/sdc
root@localhost:~# pvcreate /dev/sdd
```

Initially, these hard drives won't appear to be any different. However, there is now a small block of data, called a header, in the very beginning of each that defines each device as a physical volume

The next step is to create a volume group that consists of these three physical volumes. This can be accomplished with the following command:

```
root@localhost:~# vgcreate vol1 /dev/sdb /dev/sdc /dev/sdd
```

\


![](<../.gitbook/assets/image (6).png>)

> Additionally, if another physical volume was added to the system (add a new hard drive, use the <mark style="color:red;">`pvcreate`</mark> command, and then a command called <mark style="color:red;">`vgextend`</mark>)

Any of the space in the `vol1` volume group can be used to create a logical volume with a command like the following:

```
root@localhost:~# lvcreate -L 200M -n logical_vol1 vol1
```

The `-L` option is used to specify the size of the logical volume. The `-n` option is used to provide a name to the logical volume. The resulting name of the logical volume created by the previous command will be `logical_vol1`

The previous `lvcreate` command would result in a new device name of `/dev/vol1/logical_vol1` that could be used just like a traditional partition.

![](<../.gitbook/assets/image (17).png>)

{% hint style="info" %}
For Red Hat-based systems, the graphical tool <mark style="color:red;">`system-config-lvm`</mark> not only simplifies the process of working with LVM, it also helps to visualize what is happening:
{% endhint %}

## Creating a FileSystem

If the <mark style="color:red;">`fdisk`</mark> command is used to create a partition, then the filesystem will have to be created separately.

**The **<mark style="color:red;">**`mkfs`**</mark>** command can be used to create a filesystem**. To make a **vfat** type filesystem, which is compatible with multiple operating systems, including Microsoft Windows, execute a command like the following:

```
mkfs -t vfat /dev/sdb1
```

The <mark style="color:red;">`mkfs`</mark> command is a wrapper that executes another command, which will actually make the correct filesystem. When provided the <mark style="color:red;">`-t vfat`</mark> option, the <mark style="color:red;">`mkfs`</mark> command will call the <mark style="color:red;">`mkdosfs`</mark> command to make the actual filesystem.

A very common filesystem is the **Fourth Extended Filesystem, ext4,** the default filesystem on many Linux distributions. To create this type of filesystem, execute a command like:

```
mkfs -t ext4 /dev/sdb1
```

In this case, the <mark style="color:red;">`mkfs`</mark> command would end up calling the <mark style="color:red;">`mke2fs`</mark> command which is capable of creating ext2, ext3, and ext4 type filesystems.

| Option | Description                                                                                                                                                                      |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-b`   | Specifies the block size of the filesystem. While the default is typically fine for normal filesystems, for filesystems with large databases, a larger block size is more ideal. |
| `-N`   | Specifies the number of inodes.                                                                                                                                                  |
| `-m`   | Specifies what percentage of the filesystem is reserved for system use. C                                                                                                        |

## exFAT

The exFAT (Extended File Allocation Table) filesystem is a propietary, lightweight filesystem **created by Microsoft** for <mark style="background-color:red;">use with flash memory storage systems like SD cards and USB thumbs</mark> drives.

It was designed to replace FAT32, and it greatly expands the amounts allowed for files and directories.&#x20;

{% hint style="danger" %}
**Linux does not natively support exFAT.**
{% endhint %}

To demonstrate how to install the necessary packages in order to access the exFat drive, consider the image below, which shows a 63gb USB drive with the Ubuntu Disks application.

From the command line, you will need to make sure the proper repository, in this case, the `universe` repository, is installed. To ensure that the repository is installed, the `add-apt` command can be used.

```
sysadmin@localhost:~$ sudo add-apt-repository universe
```

Next, the `apt update` command can be used to confirm that everything in the repository is up-to-date:

Next, the needed `exfat-fuse` and `exfat-utils` packages will need to be installed using the `apt install` command.

## BTRFS

Is a Linux native filesystem created by Oracle and developed by multiple companies asa well as many individual contributors.

#### Copy-On-Write

BTRFS is a copy-on-write (COW) system. The main point of copy on write is that **BTRFS will not overwrite an existing file with updates.**

Copy-on-write is one reason why BTRFS has a significant advantage of recovery time from hardware failure, power outage, or other forms of catastrophe.

#### Snapshots

BTRFS's use copy-on-write also makes snapshots possible and very useful to the system operator.

BTRFS does this differently, it declares a reference to the original as a snapshot and if nothing is changed, effectively takes no additional space.

When a change is made after a snapshot, the changes are noted and logged, but only that change is actually made as a copy, all of the rest of the filesystem does not need to be copied too.

#### Subvolumes

Handled by the BTRFS volume manager. The basic concept of BTRFS being able to base its volumes/filesystems on multiple physical devices.

#### Compression

BTRFS also features compressions options that can be turned on via mounting options, with the compres option using several levels or types, or no compression at all.

## Creating a Swap Space

Swap space (also called virtual memory) is hard drive space that can be used by the kernel and memory management routines to store data that is normally stored in RAM. When RAM starts to become full, the kernel will take some of this data and swap it to the hard drive. At a later time, as it's needed, the data will be swapped back to RAM.

Even if the system has plenty of RAM, **creating a swap space is still useful because if the system ever crashes, swap space is used to store a **_**crash dump file**_ which is used by advanced system admins to determine why the system crashed.

There are two types of swap spaces that can be created:

* **Swap partition:** The more common of the two, a swap partition is a partition that doesn't have regular filesystem on it, and is not mounted.
* **Swap file:** In the event that there is no unpartitioned space left on the hard drive, a swap file can be used. Swap files are more flexible and can be created on the fly without the need to repartition the hard drive.

### Creating a Swap Partition

The steps to creating a swap partition are:

1.  Create a partition with an ID of 82 using `fdisk`&#x20;

    ```
    Command (m for help): n
    Command Action
       e   extended
       p   primary partition (1-4)
    p
    Partition number (1-4): 3
    First sector (20971520-21995519, default 20971520):
    Using default value 20971520
    Last sector, +sectors, or +size{K,M,G} (20971520-21995519, default 21995519): +100

    Command (m for help): t
    Partition number (1-6): 3
    Hex code (type L to list codes): 82
    Changed system type of partition 3 to 82 (Linux swap / Solaris)
    ```

2\. Convert the partition to swap space with the <mark style="color:red;">`mkswap`</mark> command

```
root@localhost:~# mkswap /dev/sda3
Setting up swapspace version1, size = 102396 KiB
no label, UUID=59aaf06e-7109-471f-88a5-e81dd7c82d76
```

3\. Enable the partition as current swap space with the <mark style="color:red;">`swapon`</mark> command

1. ```
   root@localhost:~# swapon /dev/sda3
   ```

The <mark style="color:red;">`-s`</mark> option to the <mark style="color:red;">`swapon`</mark> command will display currently used swap space

```
root@localhost:~# swapon -s
Filename                                Type            Size    Used    Priority
/devdm-1                                partition       1015800 0       -1
/dev/sda3                               partition       102392  0       -2
```

### Creating a Swap File

1.  Create a large file using the `dd` command. In order to determine which filesystem has room for the swap file, the `df` command was executed.

    ```
    root@localhost:~# dd if=/dev/zero of=/var/extraswap bs=1M count=100
    100+0 records in
    100+0 records out
    104857600 bytes (105 MB) copied, 0.320096 s, 328 MB/s
    ```

Note that the resulting file is approximately 100MB in size, 100 blocks of 1MB in size. The options `bs=100M` and `count=1` would have resulted in the same size. The file is full of binary zero values that came from the `/dev/zero` file. What is actually in the file doesn't really matter; the size of the file is what is important.

2\. Convert the file to swap space with the `mkswap` command.

```
root@localhost:~# mkswap /var/extraswap
Setting up swapspace version 1, size = 102396 KiB
no label, UUID=908e51f8-a022-4508-8819-73e1d8837e2b
```

Enable the file as current swap space with the `swapon` command

```
swapon /var/extrswap
```
