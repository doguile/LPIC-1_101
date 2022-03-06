# Chapter 22: Maintaining Integrity

<details>

<summary>Key terms</summary>

`df` Command used to report file system disk space usage.&#x20;

`du` Command used to estimate file usage on a disk.&#x20;

`tune2fs` Command that allows a system administrator to adjust various tunable filesystem parameters such as mount count and maximum mount counts before a file system check needs completed

</details>

## Filesystem Issues

There are a few commands available to shut down the Linux system from the command line. For example , the following `init` commands and user commands are available.

| Init Command | Purpose             | User Command |
| ------------ | ------------------- | ------------ |
| `init 0`     | Shut the system off | `halt`       |
| `init 6`     | Restart the system  | `reboot`     |

{% hint style="danger" %}
**Note**

Linux distributions that have replaced the traditional SysVinit daemon with systemd use the `systemctl` command to affect the state of the system and services running. To restart a system using systemd, execute the <mark style="color:red;">`systemctl reboot`</mark> command. To shut down a system using systemd, execute using the <mark style="color:red;">`systemctl poweroff`</mark> command.
{% endhint %}

## Monitoring Disk Info

Two storages-related statistics that should be monitored closely to ensure a system will continue to run correctly are free **data block space** and **free inodes**. The <mark style="color:red;">`df`</mark> command can help monitor both of these important numbers.

Data block space is used to store the contents of a file (the file's data)

Each file created in a filesystem is assigned a unique identifier by the filesystem. This identifier is know as an _inode_ and there is a **fixed number** of them available when using ext2/ext3/ext4.

{% hint style="info" %}
Inodes are important because in order to create a file**, an inode is needed to store the metadata about the file**. In fact, inodes stores everything about the file excepts its name and its contents.
{% endhint %}

To determine how many files have been created and how many inodes are still available for a filesystem, use the <mark style="color:red;">`-i`</mark> option to the `df` command.

```
sysadmin@localhost:~$ df -i
Filesystem    	Inodes     IUsed     IFree IUse% Mounted on
overlay    	235708416 502951 235205465    1% /
tmpfs       	16501830      19  16501811    1% /dev
tmpfs       	16501830      14  16501816    1% /sys/fs/cgroup
```

## The `du` command

One of the next logical steps to address this concern would be to figure out what directory or subdirectory within a filesystem might be using the most data blocks.

The _directory_ usage <mark style="color:red;">`du`</mark> command will report the size of files and directories; this can be helpful for finding the largest ones.

The default output of the `du` command shows just two columns of information: **the file size and the path name for the file**. The `du` command is automatically recursive, so it will normally process not only the current directory but also all of its directory.

```bash
sysadmin@localhost:~$ du  | sort  -n  | tail  -10
4   	./Pictures
4   	./Public
4   	./Templates
4   	./Videos
8   	./Documents/School/Art
8   	./Documents/School/Engineering
```

Two options used often with the `du` command are the _human-readable_ <mark style="color:red;">`-h`</mark> option and the _summary_ <mark style="color:red;">`-s`</mark> option to only display a summary of the entire directory. For example, executing the command below would display a human-readable summary of how much space is used by the `/bin` directory.

```
sysadmin@localhost:~$ du -sh /bin /user/bin
6.6M	/bin
```

A couple of other options to the `du` command that can be useful include the <mark style="color:red;">`--max-depth`</mark> and  <mark style="color:red;">`--exclude`</mark> options

To limit the depth of how recursive the `du` command will be, provide a numerical argument to the <mark style="color:red;">`--max-depth`</mark> option to indicate how many directories deep to display.

```bash
sysadmin@localhost:~$ du --max-depth=1 Documents
28      Documents/School
4       Documents/Work
1128    Documents
```

If there are files or directories that shouldn't be included, use one or more `--exclude` options. Glob characters can be used as well to form the pattern to be used by the `--exclude` option.

```bash
sysadmin@localhost:~$ du --exclude=School Documents
4       Documents/Work
1100    Documents
```









