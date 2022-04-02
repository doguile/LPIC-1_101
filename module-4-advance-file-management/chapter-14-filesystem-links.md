---
description: '104.6: Create and change hard and symbolic links v2  Weight: 2'
---

# Chapter 14: Filesystem Links

<details>

<summary>Key terms</summary>

<mark style="color:red;">**`ln`**</mark> Command used to create a link between two files. Either hard links or soft links

<mark style="color:red;">**`ls`**</mark> Command that will list information about files.

</details>

Linux provide two different kinds of links for files within the filesystem: hard links and soft links. Both provide the ability to have more than one path name that refers to the same file.

## Soft Links

Soft links, **also known as **_<mark style="color:red;">**symbolic links**</mark>_, are files that are designed to **point to other files via path names**. For example, the `/bin/systemd` file is a symbolic link that links to the `/lib/systemd/systemd`

Consider soft links like "Shortcuts" located on most desktops and menu bars, that when clicked will run a program or open a file. Those icons are actually files that point to other files.

Symbolic links on the system are **stored in the `/etc/alternatives`** directory where they are managed by the alternatives system. The alternatives system manages, maintains , and updates information about the symbolic links so that system administrators can define which program and version are run when a user types a generic command.

Soft links are very visual compared to hard links. This is because soft links are distinguishable by their file type.

For example, a detailed listing of the `/bin/systemd` file shows that it is a symbolic link that points to the `/lib/systemd/systemd` file:

```
sysadmin@localhost:~$ ls -l /bin/systemd
lrwxrwxrwx 1 root root 20 Feb 28 21:03 /bin/systemd -> /lib/systemd/systemd
```

In a detailed listing of a symbolic link, notice that the first character preceding the permissions is the letter `l`

```
lrwxrwxrwx 1 root root 20 Feb 28 21:03 /bin/systemd -> /lib/systemd/systemd  
```

The other thing to notice about listing a soft link is that the link file name is followed by an arrow, which points to a path name:

```
lrwxrwxrwx 1 root root 20 Feb 28 21:03 /bin/systemd -> /lib/systemd/systemd
```

The permissions that appear on the soft link only determine who may attempt to follow the link. Typically, the permissions on the soft link are `rwx` for all users. The permissions on the file that has been linked to will determine the actual or effective permissions that a user will have, if they attempt to follow the link.

For example, the permissions on the `/bin/systemd` file are `rwxrwxrwx` ,which indicate that everyone would have full access on the link file.&#x20;

However, examining the permissions on the `/lib/systemd/systemd` file shows that only the root user would have _write_ access to the file:

```
sysadmin@localhost:~$ ls -l /lib/systemd/systemd
-rwxr-xr-x 1 root root 1595792 Feb 28 21:03 /lib/systemd/systemd
```

To create a soft link file, ** **<mark style="background-color:blue;">**use the**</mark><mark style="background-color:blue;">** **</mark><mark style="background-color:blue;"><mark style="color:red;">**`ln`**<mark style="color:red;"></mark><mark style="background-color:blue;">** **</mark><mark style="background-color:blue;">**command with the**</mark><mark style="background-color:blue;">** **</mark><mark style="background-color:blue;"><mark style="color:red;">**`-s`**<mark style="color:red;"></mark><mark style="background-color:blue;">** **</mark><mark style="background-color:blue;">**option**</mark>. **The first argument is the original file name**, and the second argument is the name of the link to be created.

```
ln -s TARGET LINK_NAME
```

Be sure to put the arguments in the correct order, as it is not possible to create a link from a file name that already exists.

```
sysadmin@localhost:~$ ln -s file1.txt file2.txt
sysadmin@localhost:~$ ls -l file*
-rw-rw-r-- 1 sysadmin sysadmin 0 May  9 02:48 file1.txt
lrwxrwxrwx 1 sysadmin sysadmin 9 May  9 02:49 file2.txt -> file1.txt
```

Unlike hard links, soft link files do not increase the link count number associated with a regular file. The link count number for the ./file1.txt file would stat at one, regardless of how many soft or symbolic link files were created to refer to that file.&#x20;

```
-rw-r--r-- 1 sysadmin sysadmin May 9 14:39 file1.txt
lrwxrwxrwx 1 sysadmin sysadmin May 9 14:39 file2.txt -> file1.txt
```

## Hard Links

Hard links are just like regular files expect that **they share an inode with another file**. This means that althought the path names for the hard linked files may be different, everything else about them is identical.&#x20;

To understand hard links, it is helpful to understand a little bit about how the file system keeps track of files. For every file created, there is a block of data on the file system that stores the metadata of the file.

{% hint style="info" %}
Metadata includes information about the file like the permissions, ownership, and timestamps. Metadata does not include the file name or the contents of the file, but it does include just about all other information about the file.
{% endhint %}

This metadata is called the file's _inode table_. The inode table also includes pointers to the others blocks on the file system called data blocks where the data is stored.

Every file on a partition has a unique identification number called an inode number. The <mark style="color:red;">`ls -i`</mark> command displays the inode number of a file.

```bash
sysadmin@localhost:~$ ls -i ~/Documents/linux.txt                               
73798439 /home/sysadmin/Documents/linux.txt
```

The inode table does not include the file name. For each file, there is also an entry that is stored in a directory's data area (data block) that includes an association between an inode number and a file name.

When you execute the `ls -li` command, the number that appears for each file between the permissions and the user owner is the link count number.&#x20;

```
sysadmin@localhost:~$ ls -li file.original
278772 -rw-rw-r--. 1 sysadmin sysadmin 5 Oct 25 15:42 file.original
```

The link count number indicates how many hard links have been created. When the number is a value of one, then the file has only one name linked to the inode.

To create hard links, the <mark style="color:red;">`ln`</mark> command is used with two arguments (and without the `-s` option). The first argument is an existing file name to link to, called a _target_, and the second argument is the new file name to link to the target.&#x20;

```
ln TARGET LINK_NAME
```

When a hard link is created, the link count value is increased by a value of one. For example, the following example demonstrates using the `ln` command to create a hard link file. Notice the link count (the number after the permissions) before and after the hard link was created:

```
sysadmin@localhost:~/Documents$ ls -l profile.txt
-rw-r--r-- 1 sysadmin sysadmin 110 Apr 24 16:24 profile.txt
sysadmin@localhost:~/Documents$ ln profile.txt myprofile.txt
sysadmin@localhost:~/Documents$ ls -l profile.txt
-rw-r--r-- 2 sysadmin sysadmin 110 Apr 24 16:24 profile.txt
```

When viewing hard link files, the `ls -i` option can be helpful to validate that the files are in fact sharing an inode, as the option causes the inode number to be displayed in front of the permissions.

```
sysadmin@localhost:~/Documents$ ls -li profile.txt myprofile.txt
95813671 -rw-r--r-- 2 sysadmin sysadmin 110 Apr 24 16:24 myprofile.txt
95813671 -rw-r--r-- 2 sysadmin sysadmin 110 Apr 24 16:24 profile.txt
```

## Soft Links vs. Hard Links

### Hard Link Advantages

* Hard links are indistinguishable by programs from regular files.
* If files are hard linked, then they are always contained within one filesystem.
* Hard link don't have a single point of failure.

Once files are hard linked together, there is no concept of the original. Each file is equal, and if one link is deleted, the others still work, you don't lose the data. As long as one hard link remains, the file data can be accessed.

* Hard links are easier to locate.

A regular file with a hard link count value greater than one is a file that has a hard link. Using the file's inode number, it is possible to find all the linked files by using the `find` command with the   `-inum` option.

```
sysadmin@localhost:/bin$ cd ~/Documents
sysadmin@localhost:~/Documents$ ls -i words
48365918 words
sysadmin@localhost:~/Documents$ find -inum 48365918
./words
./mywords
```

### Soft link Advantages

* Soft links can be made to a directory file; hard links cannot.
* Soft links can link to any file

Soft link can be made from a file on one filesystem to a file on another filesystem; hard links cannot.&#x20;

* Soft links are easier to see

In general, if you need to link to a file on another filesystem or to a directory, then soft links are the correct type to use. Otherwise, you should make use of hard links.

