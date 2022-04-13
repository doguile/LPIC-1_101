---
description: '104.3: Control mounting and unmounting of filesystems v2  Weight: 3'
---

# Module 7: Administration of Filesystem

## Chapter 21: Mounting Filesystems

<details>

<summary>Key Terms</summary>

**`/etc/fstab`** A file that contains descriptive information about various file systems.

**`/media/`** The directory containing subdirectories used as mount points for removable media such as floppy disk, cdroms, dvds, and flash drives

**`blkid`** Command that can be used to determine the UUID value for device

**`fsck`** Command used to check and optionally repair one or more Linux file systems

**`lsblk`** Command that lists information about block devices.

**`mount`** Command used to tell the Linux kernel to attach a files system found on a device to a certain directory

**`umount`** Comman used to tell the Linux kernel to detach a file system found on a device from a certain directory.



</details>

Recalls the 3 steps in the process of making and using filesystems:

1. **Divide** the hard drive **into** **partitions**.
2. **Format** each **partition** with a **filesystem**
3. **Mount** the formatted **partitions** onto the directory tree of an **existing filesystem**

This chapter will focues on the final step of mounting the formatted partition.

## Viewing Mounted Filesystem

**Mounting of partitions and checking on existing mounts** is accomplished with the <mark style="color:red;">**`mount`**</mark> command. When called with no arguments, the <mark style="color:red;">**`mount`**</mark> command **shows the currently mounted devices.**

```bash
sysadmin@localhost:~$ mount
/dev/sda2 on / type ext4 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
```

The general format of this output is:

```
DESCRIPTION OF SOURCE on MOUNT POINT type FILESYSTEM TYPE (OPTIONS)
```

#### Description of Source

Every mounted filesystem comes from source -- either a device representing a disk, a network location for a network filesystem, or a tag indicating a pseudo-filesystem.

```
/dev/sda5
```

#### Mount point

The mount point shows where the source can be found on the filesystem,

```
on / 
on /home
```

#### Filesystem type

Each filesystem has a type, which tells the kernel how to work with the data contained on the device.

```
type ext4
type proc 
```

#### Options

Each filesystem also has options. Some options, such as the read/write `rw` option and read-only `ro` option, are fairly universal.

```
ext4 (rw)
(rw,gid=5,mode=620)
```

**Another method for getting information about filesystems** is by using the <mark style="color:red;">**`lsblk`**</mark> **command**. The <mark style="color:red;">**`lsblk`**</mark> command **list information about block devices**, either all of the ones that are available with the <mark style="color:red;">**`-a`**</mark> option, or output can be tailored with various other options such as the <mark style="color:red;">**`-f`**</mark> option, which output information about available filesystems.

```bash
sysadmin@localhost: lsblk -a
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0    7:0    0        0 loop
loop1    7:1    0        0 loop
loop2    7:2    0        0 loop
loop3    7:3    0        0 loop

sysadmin@localhost:/$ lsblk -f
NAME   FSTYPE LABEL UUID MOUNTPOINT
sda
|-sda1
|-sda2                   /etc/hosts
`-sda3                   [SWAP]
```

## Mounting Filesystems Manually

In order to properly mount a filesystem, at least two parameters must be provided to the <mark style="color:red;">**`mount`**</mark> command: **the filesystem identifier** and **the directory path name** to mount it on.&#x20;

The **first step** to mounting a filesystem is to **create a directory to serve as a mount point**. Alternatively, the <mark style="color:red;">**`/mnt`**</mark> and <mark style="color:red;">**`/media`**</mark> directories **may be used**, which usually exist by default on most Linux distributions for this purpose. <mark style="background-color:red;">**The**</mark><mark style="background-color:red;">** **</mark><mark style="background-color:red;">**`/mnt`**</mark><mark style="background-color:red;">** **</mark><mark style="background-color:red;">**and**</mark><mark style="background-color:red;">** **</mark><mark style="background-color:red;">**`/media`**</mark><mark style="background-color:red;">** **</mark><mark style="background-color:red;">**directories are typically only used to mount a temporary resource**</mark> such as removable media, including USB drives and optical disks.

The easiest form of the manual <mark style="color:red;">**`mount`**</mark> command is shown below. In this situation, the Linux kernel understands that **`/dev/sdb`** is a filesystem and can even detect what kind of filesystem is on the disk.

```bash
mount /dev/sdb /mnt
```

If the **filesystem cannot be detected**, then use the <mark style="color:red;">**`-t`**</mark> option to **indicate the type of filesystem**

```bash
mount -t iso9600 /dev/sdc0 /mount
```

This last command mounts a device called **`/dev/sdc0`** on **`/mnt`** and tells the kernel to use the **`iso9600`** filesystem, which is for CD and DVD devices.

{% hint style="danger" %}
If it is necessary to tell the kernel what kind of filesystem is being used, then this should be a sign that something is amiss as the autodetection is generally reliable.
{% endhint %}

When the filesystem is mounted, the default options for that filesystem are used. The <mark style="color:red;">**`-o`**</mark> **option** can be **used to** **specify alternative mount options** from the command line.&#x20;

To mount a filesystem `read-only`, pass the <mark style="color:red;">**`ro`**</mark> option, such as:

```bash
mount /dev/sdb2 /opt -o ro
```

{% hint style="info" %}
**Consider This**

Mounting a filesystem on a directory that already contains files will render those files temporarily inaccessible. Therefore be careful to mount on directories known to be empty.

Once a filesystem is mounted on a directory, only the contents of that filesystem will be visible. What was previously visible in a directory is not gone, but will become visible only after the new filesystem is unmounted.
{% endhint %}

## Unmounting Filesystems Manually

Unmounting is the procedure **used to flush all the buffers** and make the disk safe to remove.

**To unmount a filesystem, use the **<mark style="color:red;">**`umount`**</mark>** command** with either the filesystem or the directory given as an argument. For example, to unmount the `/mnt` directory that was previously mounted, execute the following command

```bash
umount /mnt
```

Because the `/dev/sdb1` filesystem was mounted under the `/mnt` directory, the following command would have also worked:

```bash
root@localhost:~ umount /dev/sdb1
```

There are few instances where the `umount` command will fail to unmount a filesystem:

* There is a **file open** (used by a program) that is located within the filesystem
* There is an **executable file running** that is located within the filesystem
* A user has a **directory within the filesystem** as their current working directory.

When the <mark style="color:red;">**`umount`**</mark> command fails to unmount a filesystem, it will normally report that the device is busy.

```
root@localhost:~# umount /mnt
umount: /mnt: device is busy.
	(In some cases useful info about processes that use
	 the device is found by lsof(8) or fuser(1))
```

To determine **what is making the filesystem busy**, use the <mark style="color:red;">**`lsof`**</mark> of <mark style="color:red;">**`fuser`**</mark> **commands**.

The output of the <mark style="color:red;">**`lsof`**</mark> command is the list of all open files on the operating system. Therefore, it is important to filter the output of the <mark style="color:red;">**`lsof`**</mark> command to find the correct opened file.

```bash
root@localhost:/mnt: lsof | grep /mnt
bash      2577      root  cwd       DIR        8,1     1024          2 /mnt
lsof      2631      root  cwd       DIR        8,1     1024          2 /mnt
```

> The first 3 columns are most useful; the name of the process, the process ID and the user running the process.

The <mark style="color:red;">**`fuser`**</mark> **command provides much more concise output**, and it will indicate if a file is open for writing. It is even to terminate processes, much like the **`kill`** command.

The <mark style="color:red;">**`-v`**</mark> option to the <mark style="color:red;">**`fuser`**</mark> command produces slightly more output, including the name of the user who is running the process and a code which indicates the way that a process is using the directory.&#x20;

For example, if unsuccessfully attempting to unmount the **`/mnt`** directory, try executing the following <mark style="color:red;">**`fuser`**</mark> command

```bash
root@localhost:~ fuser -v /mnt
                     USER        PID ACCESS COMMAND
/mnt:                root       2529 ..c.. bash
```

In this case, the process keeping the **`/mnt`** directory busy is being run by the root user. The process id is **`2529`**, the access of **`c`** indicates the process is using the directory as its current directory. The process itself is the Bash shell.

Access codes that might be reported by the <mark style="color:red;">**`fuser`**</mark> command are:

| Access Code | Meaning                                                                            |
| ----------- | ---------------------------------------------------------------------------------- |
| `c`         | The process is using the mount point or a subdirectory as its current directory.   |
| `e`         | The process is an executable file that resides in the mount point structure.       |
| `f`         | The process has an open file from the mount point structure.                       |
| `F`         | The process has an open file from the mount point structure that it is writing to. |
| `r`         | The process is using the mount point as the root directory.                        |
| `m`         | The process is a mmap'ed file or shared library.                                   |

If the process has a file open for writing or the mount point is a root directory, it is not wise to kill that process. Otherwise, to terminate the process, execute the following:

```bash
fuser -k /mnt
```

As with the **`kill`** command, the default signal sent by the <mark style="color:red;">**`fuser`**</mark> command is TERM signal. Use the <mark style="color:red;">**`fuser -l`**</mark>** command to list the signals**.

```bash
root@localhost:~ fuser -l
HUP INT QUIT ILL TRAP ABRT IOT BUS FPE KILL USR1 SEGV USR2 PIPE ALRM TERM STKFLT CHLD CONT STOP
```

These are the same signals that are available for the <mark style="color:red;">**`kill`**</mark> command. To send a specific signal with the <mark style="color:red;">**`fuser`**</mark> command, place a hyphen in front of the signal name.

For example, to end a stubborn process that is keeping the **`/mnt`** directory busy, execute the following command:

```
fuser -k -KILL /mnt
```

{% hint style="info" %}
**The **<mark style="color:red;">**`fuser`**</mark>** command is designed to stop processes so a filesystem can be unmounted**. Because filesystems typically require root privileges to unmount, it would seem to be unusual for a regular user to execute either the `fuser` or `umount` commands.
{% endhint %}

## Mounting Filesystems automatically on Boot

Manually mounting filesystems with the <mark style="color:red;">**`mount`**</mark> **command results in a** **non-persistent mount**. If the system is rebooted, then the filesystem must be mounted again to be accessed. The same is true for activating swap devies with the <mark style="color:red;">**`swapon`**</mark> command.

**The** <mark style="color:red;">**`/etc/fstab`**</mark> **file is used to configure what filesystems will be mounted automatically on boot**. It can also be used to activate swap devices automatically.&#x20;

> It may be helpful to think of this file as the **filesystem table**, to remember its name. **Root privileges are required to make changes to the `/etc/fstab` file.**

A system with three filesystem partitions and one swap partition might have an `/etc/fstab` file that looks like:

```bash
#
# /etc/fstab
# Created by anaconda on Fri Jan 17 10:31:51 2014
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /      ext4    defaults        1 1
UUID=09d641d5-bc5a-4065-8d80-8ae797dfa7f3 /boot  ext4    defaults        1 2
UUID=5ee634a5-c360-4211-a41b-9aa40d78a804 /home  ext4    defaults        1 2
UUID=34819281-65e3-4c78-ba2d-16952684c9cb swap   swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
```

### Device Identifier

#### UUID

The **first column identifies the device to be mounted.** The most common way to refer to a partition is by using a _universally unique identifier (UUID)_, as an in the following example:

```bash
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /      ext4    defaults        1 1
UUID=09d641d5-bc5a-4065-8d80-8ae797dfa7f3 /boot  ext4    defaults        1 2
```

To **determine what the UUID value is for a device**, use the <mark style="color:red;">**`blkid`**</mark> **command**:

```bash
root@localhost:~ blkid
/dev/sda1: UUID="09d641d5-bc5a-4065-8d80-8ae797dfa7f3" TYPE="ext4"
/dev/sda2: UUID="3db6ba40-67d2-403d-9c0a-9a901697cd8d" TYPE="ext4"
```

The <mark style="color:red;">**`blkid`**</mark> command can also be used with a path name for a filesystem as an argument and it will provide information about just that one device.&#x20;

```bash
root@localhost:~ blkid /dev/sdb1
/dev/sdb1: UUID="a9183c2c-ee1f-4e57-9a60-b0d9e87a0337" TYPE="ext4"
```

It is considered the best way to identify local filesystems in the **`/etc/fstab` ** file because it doesn't change after the filesystem has been created and it should always be unique.

#### Device name

A second, more traditional way of specifying a local filesystem is to use the path name for the device, such as **`/dev/sda1`** for the first partition on the first disk

```bash
/dev/sda1  /      ext4    defaults        1 1
/dev/sda2  /boot  ext4    defaults        1 2
```

#### Labels

Another options is to use filesystem labels. **The volume label can be set using the **<mark style="color:red;">**`e2label`**</mark> command.&#x20;

```bash
root@localhost:~ e2label /dev/sdb1 mydata
```

The label for a filesystem can be viewed with the <mark style="color:red;">**`e2label`**</mark> command

```bash
root@localhost:~ e2label /dev/sda1
mydata
```

Labels can also be created for the extents filesystem `xfs`, either while making the filesystem or by using the <mark style="color:red;">**`xfs_admin`**</mark> command

```
xfs_admin -L myapps /dev/sdb3
```

Labels can be viewed (if they are set) with the <mark style="color:red;">**`blkid`**</mark> command

```
root@localhost:~# blkid /dev/sdb1
/dev/sdb1: UUID="a9183c2c-ee1f-4e57-9a60-b0d9e87a0337" TYPE="ext4" LABEL="mydata"
```

**Labels can be used in the first field** of the **`/etc/fstab`** file, such as `LABEL="mydata"`. If labels are created by a single administrator, unique labels can be ensured

The following table provides a summary of these three methods of referring to the partition to mount:

| Method          | Advantages                                                                                                         | Disadvantages                                            |
| --------------- | ------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------- |
| **UUID**        | Should always be unique, preventing any mounting conflicts.                                                        | Hard to type and difficult to remember.                  |
| **Device Name** | Easy to type and remember.                                                                                         | Device names can easily change, causing mounting errors. |
| **Label**       | Easy to type and remember. Label names won't dynamically change like device names, but they are not always unique. | May not be unique like UUID naming.                      |

### Mount Point field

The **second field of the `/etc/fstab` is the mount point** (mount directory) where the partition is to be mounted.&#x20;

```bash
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /      ext4    defaults        1 1
UUID=09d641d5-bc5a-4065-8d80-8ae797dfa7f3 /boot  ext4    defaults        1 2
```

### Filesystem field

**The third filed is the filesystem type**. Enter the correct Type Code using the values provided by the following table:

| Name                       | Type Code |
| -------------------------- | --------- |
| Fourth Extended Filesystem | `ext4`    |
| Third Extended Filesystem  | `ext3`    |
| Second Extended Filesystem | `ext2`    |
| Extents Filesystem         | `xfs`     |
| File Allocation Table      | `vfat`    |
| ISO 9660                   | `iso`     |
| Universal Disc Format      | `udf`     |

### Mount Option Field

**The fourth field in the `/etc/fstab` file** is a comma-separated list of the mount options which would normally be passed to <mark style="color:red;">**`mount`**</mark> with the <mark style="color:red;">**`-o`**</mark> options. This field is **used to pass parameters to the filesystem driver**, such as to make the device read-only or to adjust timeouts.&#x20;

```bash
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /      ext4    defaults        1 1
UUID=09d641d5-bc5a-4065-8d80-8ae797dfa7f3 /boot  ext4    defaults        1 2
UUID=5ee634a5-c360-4211-a41b-9aa40d78a804 /home  ext4    defaults        1 2
```

The most common mount option is the <mark style="color:red;">**`defaults`**</mark> option. The <mark style="color:red;">**`defaults`**</mark> mount option implies a number of standard mount options are in effect. The following table list the mounts options that are in effect when <mark style="color:red;">**`defaults`**</mark> is used.&#x20;

| Mount Option | Purpose                                                                                                                                                                                | Opposite |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `rw`         | Allow reading and writing                                                                                                                                                              | `ro`     |
| `suid`       | Allow suid executes                                                                                                                                                                    | `nosuid` |
| `dev`        | Allow device files                                                                                                                                                                     | `nodev`  |
| `exec`       | Allow executable files                                                                                                                                                                 | `noexec` |
| `auto`       | Automatically mount                                                                                                                                                                    | `noauto` |
| `nouser`     | Prevent ordinary users from mounting or unmounting the partition. Using the `user` option allows non-root users to mount that particular device, which is helpful for removable media. | `user`   |
| `async`      | All writes should be asynchronous                                                                                                                                                      | `sync`   |

Using the <mark style="color:red;">**`defaults`**</mark> keyword is clearer and faster than specifying each individual option.

If multiple options are to be used, then they must be separated by a comma and have no embedded spaces. Any unspecified options will still use the default values, so using the <mark style="color:red;">**`defaults`**</mark> keyword can be thought of as a simple placeholder.

The following configuration will still be mounted in read/write mode, allow executables files, and so forth:

```bash
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /date      ext4    nosuid,nodev   1 1
```

It is interesting to note that when using <mark style="color:red;">**`defaults`**</mark> for the mount options, the only mount option that will be displayed when the <mark style="color:red;">**`mount`**</mark> command is executed will be the **`rw`** options

```bash
/dev/sda2 on / type ext4 (rw)
```

### Dump Field

```bash
1                                           2      3        4            5 6
-------------------------------------------------------------------------------
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /      ext4    defaults        1 1
UUID=09d641d5-bc5a-4065-8d80-8ae797dfa7f3 /boot  ext4    defaults        1 2
UUID=34819281-65e3-4c78-ba2d-16952684c9cb swap   swap    defaults        0 0
```

The purpose of this field is to **tell an administrator which filesystems should be backed up when using the **<mark style="color:red;">**`dump`**</mark>** command**. The administrator would execute the <mark style="color:red;">**`dump -w`**</mark> command and the resulting output would be a list of the filesystems to back up.

This is generated by filesystems that have a value of `1` in the dump field of the `/etc/fstab` file. **A value of **<mark style="color:red;">**`1`**</mark>** in this field is used for local real filesystems** ( filesystems on partitions that reside on local hard drives). **Any pseudo-filesystems, remote filesystems, or swap space entries should have a **<mark style="color:red;">**`0`**</mark>** value in this field.**

### Filesystem Check Field.

```bash
UUID=3db6ba40-67d2-403d-9c0a-9a901697cd8d /      ext4    defaults        1 1
UUID=09d641d5-bc5a-4065-8d80-8ae797dfa7f3 /boot  ext4    defaults        1 2
UUID=34819281-65e3-4c78-ba2d-16952684c9cb swap   swap    defaults        0 0
```

The sixth field is for **determining the order in which the filesystem will be checked by the **<mark style="color:red;">**`fsck`**</mark> (**F**ile **S**ystem **C**hec**K**) utility during system boot. This utility is designed to **find and fix filesystem problems.**

**The root filesystem should always have a **<mark style="color:red;">**`1`**</mark>** in this field** to indicate that it will be checked by the `fsck` program first. All other local filesystems (ext2/ext3/ext4) should have a value of 2 specified for this field, so they will be checked after the root filesystem.

{% hint style="info" %}
**Any pseudo-filesystems, remote filesystems, or swap space entries should have a `0` value** in this field. These filesystems should never be checked by the <mark style="color:red;">**`fsck`**</mark> utility.
{% endhint %}

## Mounting with <mark style="color:red;">`fstab`</mark>

The <mark style="color:red;">**`remount`**</mark> option is **useful for chaging a mounted filesystem's options without unmounting the filesystem** itself.

```bash
root@localhost:~ mount /home -o remount, noatime
```

This command will switch the **`/home`** mount to have the <mark style="color:red;">**`noatime`**</mark> option (the <mark style="color:red;">**`atime`**</mark> option shows the last time a file was accessed or read) without needing to remount the filesystem.&#x20;

Keep in mind that this will not persist across a reboot until the **`/etc/fstab`** file has been updated. Once changes have been made to the **`/etc/fstab`** file, execute something like the following to make them effective:

```bash
root@localhost:~ mount -o remount /mnt
```

{% hint style="danger" %}
_**Remounting**_ is different from executing the <mark style="color:red;">**`umount`**</mark> command on a filesystem and then executing the <mark style="color:red;">**`mount`**</mark> command on that same filesystem.
{% endhint %}

While unmounting/mounting will fail on an in-use filesystem, **remounting is really just changing the **<mark style="color:red;">**`mount`**</mark>** options and will succeed on an in-use filesystem.**

After updating the **`/etc/fstab`** file, **don't reboot the system to test the changes. Instead, use the **<mark style="color:red;">**`-o remount`**</mark>** option**. If the changes are correct, no message will be displayed, but if a mistake was made, an error like the following would be displayed:

```bash
root@localhost:~ mount -o remount /
mount: / not mounted already, or bad option
```

If there are no errors in the corresponding entry contained within the **`/etc/fstab`** file, then the <mark style="color:red;">**`mount -o`**</mark> command will produce no output.

## systemd Mount Units

Another approach to mounting filesystems us by using systemd _mount units_. **Systemd mount units are configuration files that end in **<mark style="color:red;">**`.mount`**</mark> and contain information about resources to be mounted at startup.

These files contain a Mount section that has information about filesystem mount point, and they must be named for the specific path to be mounted.

**The **<mark style="color:red;">**`systemd-mount`**</mark>** utility is the mechanism that systemd uses** to create and start a transient <mark style="color:red;">**`.mount`**</mark> file, destroy  a transient `.mount` file, or to start an `.automount` unit of the filesystem.

Below is an example of a `.mount` file used for a snap install of software. **Snaps are software installation files** that can be used across various Linux distributions instead of the specific binary installers used by Ubuntu, Red Hat and other distributions.

```bash
[Unit]

Description=Mount unit for core, revision 6673

Before=snapd.service

[Mount]

What=/var/lib/snapd/snaps/core_6673.snap

Where=/snap/core/6673

Type=squashfs

Options=nodev,ro,x-gdu.hide

[Install]

WantedBy=multi-user.target
```

## Loop Option

The <mark style="color:red;">**`loop`**</mark> option to the <mark style="color:red;">**`mount`**</mark> command is used to mount special filesystems that are stored within a file.

These files have the extension of **`.img`** or **`.iso`**, which contain complete filesystems that can be mounted with the <mark style="color:red;">**`mount`**</mark> command by using the <mark style="color:red;">**`loop`**</mark> option.

```
mount -o loop fs.img /mnt
mount -o loop crdom.iso /mnt
```

To gain access to these software packages, which all end with the `.rpm` extension, the system administrator mounts the ISO file and then copies the software packages by executing the following commands:

```bash
root@localhost:~ mount -o loop software.iso /mnt
root@localhost:~ mkdir /root/latestrpms
root@localhost:~ cp /mnt/*.rpm /root/latestrpms
```

After copying the software packages to the `/root/latestrpms` directory, the `software.iso` file can be unmounted and deleted from the system.

## Monitoring Filesystems

The <mark style="color:red;">**`df`**</mark> command **can also be used to view mounted filesystems**. The output of this command **displays the usage of the filesystem**, where it's mounted, and the **space usage of the device**.

Use the <mark style="color:red;">**`-T`**</mark> option ot have the <mark style="color:red;">**`df`**</mark> command **displays the filesystem type**:

```
root@localhost:~# df -hT
Filesystem    Type    Size  Used Avail Use% Mounted on
/dev/sda2     ext4    6.3G  3.2G  2.9G  53% /
tmpfs        tmpfs    351M   84K  351M   1% /dev/shm
/dev/sda1     ext4    485M   52M  408M  12% /boot
```

## Activating Swap Space

The following describes how to configure a new swap partition that has been created on the `/dev/sdb2` device to activate during the boot process automatically. One single line will be added at the end of the `/etc/fstab` file.

> &#x20;The assumption here is that the partition has been created by the **`fdisk`** command and the partition has been configured as a swap partition with the **`mkswap`** command.

To **verify that the swap partition or file has been added correctly**, use the <mark style="color:red;">**`swapon -a`**</mark> command; this will activat all swap entries in the /etc/fstab file.&#x20;

After using the <mark style="color:red;">**`swapon -a`**</mark> command, **verify that the swap partition or file is now activated by using the **<mark style="color:red;">**`swapon -s`**</mark>** command.**
