---
description: '104.2: Maintain the integrity of filesystems v2  Weight: 2'
---

# Chapter 22: Maintaining Integrity

<details>

<summary>Key terms</summary>

<mark style="color:red;">**`df`**</mark> Command used to **report file system disk space usage**.&#x20;

<mark style="color:red;">**`du`**</mark> Command used to **estimate file usage on a disk**.&#x20;

<mark style="color:red;">**`tune2fs`**</mark> Command that allows a system administrator to **adjust various tunable filesystem parameters** such as mount count and maximum mount counts before a file system check needs completed

</details>

## Filesystem Issues

There are a few **commands available to shut down the Linux system** from the command line. For example , the following <mark style="color:red;">`init`</mark> commands and user commands are available.

| Init Command | Purpose             | User Command |
| ------------ | ------------------- | ------------ |
| `init 0`     | Shut the system off | `halt`       |
| `init 6`     | Restart the system  | `reboot`     |

{% hint style="danger" %}
**Note**

Linux distributions that have replaced the traditional SysVinit daemon with systemd use the **`systemctl`** command to affect the state of the system and services running. **To restart a system using `systemd`**, execute the <mark style="color:red;">**`systemctl reboot`**</mark> command. To shut down a system using **`systemd`**, execute using the <mark style="color:red;">**`systemctl poweroff`**</mark> command.
{% endhint %}

## Monitoring Disk Info

Two storages-related statistics that should be monitored closely to ensure a system will continue to run correctly are free **data block space** and **free inodes**. The <mark style="color:red;">**`df`**</mark>** command** can help monitor both of these important numbers.

Data block space is used to store the contents of a file (the file's data). If data block space is exhausted within a filesystem, it will not be possible to add any new content to either new or existing files.

By default, the <mark style="color:red;">**`df`**</mark> command without any option or arguments will display filesystems usage using 1K (1024 bytes) data block size

{% hint style="info" %}
When exeute with the <mark style="color:red;">**`-h`**</mark> option, the output of the <mark style="color:red;">**`df`**</mark> command is displayed in human-readable units such as MiB and GiB.

Try adding a <mark style="color:red;">**`-T`**</mark> options to the <mark style="color:red;">**`df`**</mark> command to see the filesystem Type added as a column to the output.
{% endhint %}

```bash
sysadmin@localhost:~$ df
Filesystem     1K-blocks       	Used	Available Use%  Mounted on
overlay       3711914792   119199908  3404137348    4%  /
tmpfs         	66007320           0  	66007320    0%  /dev
tmpfs         	66007320           0  	66007320    0%  /sys/fs/cgroup
/dev/sda2     3711914792   119199908  3404137348    4%  /etc/hosts
shm                65536           0       65536    0%  /dev/shm
```

Each file created in a filesystem is assigned a unique identifier by the filesystem. This identifier is know as an _inode_ and there is a **fixed number** of them available when using ext2/ext3/ext4.

{% hint style="info" %}
Inodes are important because in order to create a file**, an inode is needed to store the metadata about the file**. In fact, inodes stores everything about the file excepts its name and its contents.
{% endhint %}

To determine how many files have been created and **how many inodes are still available** for a filesystem, use the <mark style="color:red;">**`-i`**</mark> **option** to the <mark style="color:red;">**`df`**</mark> command.

```bash
sysadmin@localhost:~$ df -i
Filesystem    	Inodes     IUsed     IFree IUse% Mounted on
overlay    	235708416 502951 235205465    1% /
tmpfs       	16501830      19  16501811    1% /dev
tmpfs       	16501830      14  16501816    1% /sys/fs/cgroup
```

## The <mark style="color:red;">`du`</mark> command

One of the next logical steps to address this concern would be to **figure out what directory or subdirectory within a filesystem might be using the most data blocks.**

The _**directory**_** usage **<mark style="color:red;">**`du`**</mark> command will **report the size of files and directories**; this can be helpful for finding the largest ones.

The default output of the <mark style="color:red;">**`du`**</mark> command shows just two columns of information: **the file size and the path name for the file**. The <mark style="color:red;">**`du`**</mark> command is automatically recursive, so it will normally process not only the current directory but also all of its directory.

An effective way to find the largest files within the specified directories is to pipe the output to the <mark style="color:red;">**`sort`**</mark> command with the <mark style="color:red;">**`-n`**</mark> option, so that the output will be sorted from smallest to largest.

```bash
sysadmin@localhost:~$ du  | sort  -n  | tail  -10
4   	./Pictures
4   	./Public
4   	./Templates
4   	./Videos
8   	./Documents/School/Art
8   	./Documents/School/Engineering
```

Two options used often with the <mark style="color:red;">**`du`**</mark> command are the _**human-readable**_**  **<mark style="color:red;">**`-h`**</mark>** option** and the _**summary**_**  **<mark style="color:red;">**`-s`**</mark>** option** to only display a summary of the entire directory. For example, executing the command below would display a human-readable summary of how much space is used by the `/bin` directory and the `/usr/bin` directory.

```bash
sysadmin@localhost:~$ du -sh /bin /user/bin
6.6M	/bin
```

A couple of other options to the <mark style="color:red;">**`du`**</mark> command that can be useful include the <mark style="color:red;">**`--max-depth`**</mark> and  <mark style="color:red;">**`--exclude`**</mark> options

To limit the depth of how recursive the <mark style="color:red;">**`du`**</mark> command will be, provide a numerical argument to the <mark style="color:red;">`--max-depth`</mark> option to indicate how many directories deep to display.

```bash
sysadmin@localhost:~$ du --max-depth=1 Documents
28      Documents/School
4       Documents/Work
1128    Documents
```

If there are files or **directories that shouldn't be included, use one or more** <mark style="color:red;">**`--exclude`**</mark> options. Glob characters can be used as well to form the pattern to be used by the `--exclude` option.

```bash
sysadmin@localhost:~$ du --exclude=School Documents
4       Documents/Work
1100    Documents
```

## The <mark style="color:red;">`dumpe2fs`</mark> Command

In addition to displaying file space by filesystem or directory structure, you also want to know how to **display techinical information about filesystems**. Execute the <mark style="color:red;">**`dumpe2fs`**</mark> command to display filesystem information

```
[root@centos~]# dumpe2fs /dev/sda1 | head
dumpe2fs 1.42.9 (28-Dec-2013)
Filesystem volume name:    <none>
Last mounted on:           /boot
Filesystem UUID:           5dac1ba0-cdbc-4ba2-99dd-17f298456819
Filesystem magic number:   0xEF53
Filesystem revision #:     1 (dynamic)
```

## The <mark style="color:red;">`tune2fs`</mark> Command

The <mark style="color:red;">**`tune2fs`**</mark> command can **view and change some of the settings for ext2, ext3, and ext4** filesystems. Generally, this command is considered safe.&#x20;

By default, each filesystem will have a full system check during the boot process either every 180 days or after 30 mounts, whichever comes first. Automatic filesystem checking can be disabled by executing the following:

```bash
root@localhost:~ tune2fs -c0 -i0 /dev/sdb1
```

The <mark style="color:red;">**`-c`**</mark> option will **change the maximum number of times that a filesystem may be mounted** before it is required to have a full filesystem check. The default value of 30 times has been disabled by setting the value to 0.

The <mark style="color:red;">**`-i`**</mark>** option will change the maximum time interval between when a filesystem is forced to have a full filesystem check**. This default value of 180 days has been disabled by setting the interval 0.

To automatically have the `acl` and `user_xattr` **mount options** apply the <mark style="color:red;">**`-o`**</mark> option **apply any time a filesystem is mounted**, execute the following command:

```
root@localhost:~# tune2fs -o acl,user_xattr /dev/sdb1
```

The <mark style="color:red;">**`-o`**</mark> option in the command above **specifies the default mount options**.

Finally, to verify the changes have been saved, **list the superblock information for the filesystem** by using the <mark style="color:red;">**`-l`**</mark> option:

```
root@localhost:~# tune2fs -l /dev/sdb1
```

To summarize, the following is a table of options available for <mark style="color:red;">**`tune2fs`**</mark> to tune a ext2, ext3, or ext4 filesystems.

| Option | Effect                                                                                                                                                                                                                                        |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-l`   | List the superblock information for a filesystem.                                                                                                                                                                                             |
| `-c`   | Change the maximum number of times that a filesystem may be mounted before it is required to have a full filesystem check. The default value is normally 30 times. This can be disabled by setting the value to 0.                            |
| `-i`   | Change the maximum time interval between when a filesystem is forced to have a full filesystem check. The default value is 180, meaning 180 days. This can be disabled by setting the interval to 0.                                          |
| `-j`   | Create a journal file for an ext2 filesystem, allowing it to be mounted as an ext3 or ext2 filesystem.                                                                                                                                        |
| `-m`   | Specify the percentage of space to be reserved for the root user or privileged processes. The default value of 5% is often unnecessarily large for large filesystems.                                                                         |
| `-o`   | Specify default mount options. By default, the RedHat derived distributions specify that `acl` and `user_xattr`options are added to filesystems created during installation. When applying multiple options, they need to be comma separated. |

There are filesystem settings that the `tune2fs`command cannot change and that have to be set at the time the filesystem is created. For the ext2, ext3, and ext4 filesystems, **the following table lists the **<mark style="color:red;">**`mke2fs`**</mark>** command options that cannot be changed after the filesystem is created:**

| Option | Effect                                                                                                                                                                                                                                                                                                                                                                         |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-b`   | Allows the block size to be specified. Valid block sizes are 1024, 2048, or 4096 bytes (some systems allow a block size of 8192). If the "wrong" block size is used, the filesystem may run out of data blocks before running out of inodes. If the average file size is smaller than 4 KiB, it may be beneficial to use a smaller block size closer to the average file size. |
| `-I`   | Allows the size of an inode to be specified. By default, `mke2fs` will make an inode size of 128 bytes. If the system uses SELinux or SambaV4, it may be beneficial to set this size to 256 bytes to allow space extended attributes that SELinux and sambaV4 use.                                                                                                             |
| `-i`   | Allows a bytes/inode ratio to be specified. To avoid either running out of data blocks while still having inodes, or vice versa, set a ratio that will be close to the average file size. As each file requires at least one inode to store its meta-information, this is a critical value.                                                                                    |
| `-N`   | If the size of the average file is unknown, but the number of files required is known, use this option to specify exactly how many inodes to create                                                                                                                                                                                                                            |
