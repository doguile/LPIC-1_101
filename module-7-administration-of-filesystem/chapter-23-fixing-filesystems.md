---
description: '104.2: Maintain the integrity of filesystems v2  Weight: 2'
---

# Chapter 23: Fixing Filesystems

<details>

<summary>Key Terms</summary>

**`e2fsck`** Command used to check the ext2/ext3/ext4 family of file systems

**`fsck`** Command used to check and optionally repair one or more Linux file systems. If no filesystems is specified with the command, fsck will check all filesystems in the `/etc/fstab` file by default.

**`xfs_db`** Command that can be used to perform debugging options and possible repairs to an XFS filesystem

**`xfs_fsr`** Filesystem Reorganizer for the `xfs` command can be used on your mounted.

**`xfs_repair`** A utility can be run in order to repair any inconsistencies in a filesystem. Only can be run on an unmounted filesystem

</details>

## The `fsck` command

The <mark style="color:red;">**`fsck`**</mark> command is used to **check filesystems for consistency issues and to repair those issues** when found.&#x20;

> The **`fsck`** command has some similarities to the **`mkfs`** command.

Its purpose is to **check filesystems for errors and attempt to repair them**. Second, <mark style="color:red;">**`fsck`**</mark> is actually a front end for the various filesystem checkers, meaning it will call the appropiate back end command, like the <mark style="color:red;">`e2fsck`</mark> command, based upon the filesystem type.

{% hint style="info" %}
The files that link the front end to the back end are named by patterns like the **`fsck.FILESYSTEM-TYPE`** pattern, such as `fsck.ext4` or `fsck.vfat`
{% endhint %}

For example, to discover the actual filesystem-specific command that is used to check <mark style="color:red;">`vfat`</mark> filesystem, execute the <mark style="color:red;">`man fsck.vfat`</mark> command. This will display the man page for the real back end command for checking vfat filesystems, which is the <mark style="color:red;">**`dosfsck`**</mark> command.

Likewise, viewing the page for the **`fsck.ext4`** displays the **`e2fsck`** command used for checking ext2, etx3 and ext4 filesystem.

{% hint style="warning" %}
If the filesystem does not have an entry in the **`/etc/fstab`** file, then the <mark style="color:red;">**`-t`**</mark> option can be used with the <mark style="color:red;">**`fsck`**</mark> command to **specify the filesystem type**
{% endhint %}

### Warning about the `fsck` command

The <mark style="color:red;">**`fsck`**</mark> utility is **not designed to be executed on currently used filesystems** (systems that are already mounted)

{% hint style="danger" %}
Keep in mind, if the filesystem is already mounted, then it doesn't need to be checked. The very fact that it is mounted means it is working correctly.
{% endhint %}

One of the more useful options for the <mark style="color:red;">**`fsck`**</mark> command is the <mark style="color:red;">**`-y`**</mark> option, which will cause <mark style="color:red;">`fsck`</mark> to **assume a yes response to all prompts** that it would normally make.

Another non-interactive approach is to force a filesystem check to occur, by executing the <mark style="color:red;">**`touch /forcefsck`**</mark> command as the root user. If the file exists at boot time, then the `fsck` command is executed on all filesystems in the `/etc/fstab` file that have non-zero value for the FSCK column.

A **filesystem check can be forced** by using the <mark style="color:red;">**`-f`**</mark> option:

```bash
root@localhost:~ fsck -f /dev/sdb1
fsck from util-linux-ng 2.17.2
e2fsck 1.41.12 (17-May-2010)
Pass 1: Checking indoors, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/sdb1: 11/12824 files (9.1% non-contiguous), 6532/51200 blocks
```

When a partition has a problem, attempting to mount it may result in an error message like this:

```bash
mount: you must specify the filesystem type
```

Normally, the <mark style="color:red;">**`mount`**</mark> command will **read the first block of the filesystem (the superblock)** where the information about the filesystem type is kept.

In this case, use the <mark style="color:red;">**`fsck`**</mark> command to fix the filesystem.

## The `e2fsck` command

The <mark style="color:red;">**`e2fsck`**</mark> command is the filesystem checker called by <mark style="color:red;">**`fsck`**</mark> **for ext2, ext3 and ext4 filesystems.** There are options that can be used with the <mark style="color:red;">`e2fsck`</mark> command that aren't documented in the man page for the <mark style="color:red;">`fsck`</mark> command.

For example, the <mark style="color:red;">**`fsck`**</mark> command may require the exact location of a backup superblock.&#x20;

{% hint style="danger" %}
The superblock is a critical component to the filesystem, and when corrupted, it is difficult to recover.

**The backup superblocks are used by the **<mark style="color:red;">**`fsck`**</mark>** command to fix a corrupted superblock**
{% endhint %}

If custom settings were used when creating filesystem, the backup superblocks may not be in a standard location. In that case, the output of the <mark style="color:red;">`mkfs`</mark> command should be documented because the output includes the location of the backup superblocks.

These backup **superblocks may be located** by using the <mark style="color:red;">**`dumpe2fs`**</mark> command

```bash
root@localhost:~ dumpe2fs /dev/sdb1 | grep superblock
dumpe2fs 1.41.12 (17-May-2010)
  Primary superblock at 1, Group descriptors at 2-2
  Backup superblock at 8193, Group descriptors at 8194-8194
  Backup superblock at 24577, Group descriptors at 24578-24578
  Backup superblock at 40961, Group descriptors at 40962-40962
```

Use the <mark style="color:red;">**`-b`**</mark> option with the <mark style="color:red;">**`e2fsck`**</mark> command to **specify a backup when the primary superblock has been corrupted.**

```bash
e2fsck -b 8193 /dev/sdb1
```

The <mark style="color:red;">**`fsck`**</mark> command could also be used with the same option, it will pass this options on the <mark style="color:red;">**`e2fsck`**</mark> command.

```bash
fsck -b 8193 /dev/sdb1
```

{% hint style="info" %}
The **`-n`** option assumes that the input for every prompt is _no._ The **`-n`** option can be useful when running the command to test for possible problems, but opting not to fix them at this time.
{% endhint %}

## The `lost+found` Directory

If the <mark style="color:red;">**`fsck`**</mark> command produces any unreferenced file errors, it may be prudent to look inside the <mark style="color:green;">**`lost+found`**</mark> directory that is located in the mount point directory of the filesystem. **This is the location where all lost files are stored**.

A file can be "lost" if its filename becomes corrupted and therefore cannot be placed in a normal directory. After <mark style="color:red;">**`fsck`**</mark> ,such a file is relocated to the <mark style="color:green;">**`lost+found`**</mark> directory, and its inode number will appear instead of the filename.

```bash
root@localhost:~ ls -l /data
lost+found
root@localhost:~ ls -l /data/lost+found
total 5
-rw-r--r--. 1 root root   83 Mar  4 06:37 #34234
-rw-r--r--. 1 root root 1911 May 20  2013 #4596
```

## Repairing XFS Filesystems

**The XFS file system is in wide usage**. Some distro use it for most if not all filesystems, others use it as the file system for the user's home directory portion of a system.

XFS relies on the journal for most error correction, or when dealing with the filesystem being marked as not having been properly unmounted.

{% hint style="info" %}
If a filesystem is not cleanly unmounted, it is marked as needing to be checked, which XFS does by replaying the journal log ( this is enough to ensure that the data is written correctly)
{% endhint %}

In case where XFS's metadata or structure has become corrupted, an error such as `mount: Structure needs cleaning` may occur, and the <mark style="color:red;">**`xf_repair`**</mark> utility will need to be run against the filesytem in order to **repair any inconsistencies**.

{% hint style="info" %}
**Consider this**

The <mark style="color:red;">**`xfs_copy`**</mark> and <mark style="color:red;">**`xfsdump`**</mark> commands may be helpful to backup the data.

The <mark style="color:red;">**`xfs_copy`**</mark> command can be create an exact duplicate of the questionable filesystem in order to experiment with repairs.
{% endhint %}

The <mark style="color:red;">**`xfs_repair`**</mark> command **can only be run on an unmounted filesystem**, which means if the XFS filesystem on a device is used as the `/home` directory, then all users must be off the filesystem in order to unmount it for repair.

```bash
root@localhost:~ umount /dev/sda3
```

```bash
root@localhost:~ xfs_repair /dev/sda3

Phase 1 - find and verify superblock...
Phase 2 - using internal log
    	- zero log...
    	- scan filesystem freespace and inode maps...
    	- found root inode chunk
Phase 3 - for each AG...
```

In situations where **manual repair of the XFS filesystem** is desired, the <mark style="color:red;">**`xfs_db`**</mark> **command** can be used to initiate such repair on an XFS filesystem. The <mark style="color:red;">**`xfs_db`**</mark> command is used to **perform debugging options and possible repairs to an XFS filesystem**.

To use <mark style="color:red;">**`xfs_db`**</mark> in expert mode(allow modifications of data structures) use the command:

```bash
xfs_db -x /dev/sda3
```

As a method of keeping your XFS filesystems in good shape, the <mark style="color:red;">**`xfs_fsr`**</mark> or **Filesystem Reorganizer** for the **`xfs`** command can be used on your mounted XFS filesystems to keep them organized and working well.

Before invoking the <mark style="color:red;">**`xfs_fsr`**</mark> command to use system resoruces reorganizing filesystems, it's a good idea to check if they need this sort of maintenance. The <mark style="color:red;">**`xfs_db`**</mark> command can be used to **assess the need for reorganization**

```
xfs_db -r /dev/sda3
xfs_db>frag
actual 345, ideal 289, fragmentation factor 16.23%
Note, this number is largely meaningless.
Files on this filesystem average 1.19 extents per file
xfs_db>quit
```

**To reorganize and optimize all mounted filesystems** on your system and see verbose output on exactly what is being done to what files, use the command:

```bash
xfs_fsr -v /dev/sda3
```

Another very useful feature of the <mark style="color:red;">**`xfs_fsr`**</mark> command is that **you can set it to run for a given period of time**, so it can be started either manually or on a scheduled basis to run for a couple of hours each night when the system is less utilized. To run <mark style="color:red;">**`xfs_fsr`**</mark> for 1 hour and then stop:

```
xfs_fsr -t 3600
```
