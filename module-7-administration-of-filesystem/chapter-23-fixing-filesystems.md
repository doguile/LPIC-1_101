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

> The `fsck` command has some similarities to the `mkfs` command.

Its purpose is to **check filesystems for errors and attempt to repair them**. Second, <mark style="color:red;">`fsck`</mark> is actually a front end for the various filesystem checkers, meaning it will call the appropiate back end command, like the `e2fsck` command, based upon the filesystem type.

{% hint style="info" %}
The files that link the front end to the back end are named by patterns like the **`fsck.FILESYSTEM-TYPE`** pattern, such as `fsck.ext4` or `fsck.vfat`
{% endhint %}

For example, to discover the actual filesystem-specific command that is used to check vfat filesystem, execute the <mark style="color:red;">`man`</mark><mark style="color:red;">` `</mark><mark style="color:red;">**`fsck.vfat`**</mark> command. This will display the man page for the real back end command for checking vfat filesystems, which is the <mark style="color:red;">**`dosfsck`**</mark> command.

Likewise, viewing the page for the **`fsck.ext4`** displays the **`e2fsck`** command used for checking ext2, etx3 and ext4 filesystem.

{% hint style="warning" %}
If the filesystem does not have an entry in the `/etc/fstab` file, then the <mark style="color:red;">**`-t`**</mark> option can be used with the <mark style="color:red;">**`fsck`**</mark> command to **specify the filesystem type**
{% endhint %}

### Warning about the `fsck` command

The <mark style="color:red;">**`fsck`**</mark> utility is not designed to be executed on currently used filesystems (systems that are already mounted)

{% hint style="danger" %}
Keep in mind, if the filesystem is already mounted, then it doesn't need to be checked.
{% endhint %}

One of the more useful options for the <mark style="color:red;">**`fsck`**</mark> command is the <mark style="color:red;">**`-y`**</mark> option, which will cause `fsck` to assume a yes response to all prompts that it would normally make.

Another non-interactive approach is to force a filesystem check to occur, by executing the <mark style="color:red;">**`touch /forcefsck`**</mark> command as the root user. If the file exists at boot time, then the `fsck` command is executed on all filesystems in the `/etc/fstab` file that have non-zero value for the FSCK column.

A filesystem check can be forced by using the `-f` option:

```
root@localhost:~# fsck -f /dev/sdb1
fsck from util-linux-ng 2.17.2
e2fsck 1.41.12 (17-May-2010)
Pass 1: Checking indoors, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/sdb1: 11/12824 files (9.1% non-contiguous), 6532/51200 blocks
```
