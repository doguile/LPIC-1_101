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

Two storages-related statistics that should be monitored closely to ensure a system will continue to run correctly are free data block space and free inodes. The `df` command can help monitor both of these important numbers.

Data block space is used to store the contents of a file (the file's data)

Each file created in a filesystem is assigned a unique identifier by the filesystem. This identifier is know as an _inode_ and there is a **fixed number** of them available when using ext2/ext3/ext4.

{% hint style="info" %}
Inodes are important because in order to create a file**, an inode is needed to store the metadata about the file**. In fact, inodes stores everything about the file excepts its name and its contents.
{% endhint %}

To determine how many files have been created and how many inodes are still available for a filesystem, use the `-i` option to the `df` command.

```
sysadmin@localhost:~$ df -i
Filesystem    	Inodes     IUsed     IFree IUse% Mounted on
overlay    	235708416 502951 235205465    1% /
tmpfs       	16501830      19  16501811    1% /dev
tmpfs       	16501830      14  16501816    1% /sys/fs/cgroup
```

## The `du` command











