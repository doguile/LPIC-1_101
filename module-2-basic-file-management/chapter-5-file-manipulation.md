---
description: '103.3: Perform basic file management v2  Weight: 4'
---

# Chapter 5: File Manipulation

<details>

<summary>Key terms</summary>

**`cp`** Command used to copy files and directories

**`file`** Command used to determine the type of file. There are 3 sets of tests, performed in this order: filesystem test, magic test, and language tests

**`ls`** Command that will list information about files.

**`mkdir`** Command used to create directories, if they do not already exists.

**`rm`** Command used to remove files or directories. By default the `rm` command will not remove directories.

**`rmdir`** Command that is used to remove empty directories in the filesystem

**`touch`** Command used to change the file timestamps or allow a user to update the access and modification times of each FILE to the current time

</details>

## Introduction

Everything is considered a file in Linux. Even directories are considered a files since they are special files that are used to contain other files.

The essential commands needed for file management are often named by short abbreviations of their functions. You can use the `ls` command to list files, the `cp` command to copy files, the `mv` command to move or rename files, and the `rm` command to remove files.

For working with directories, use the <mark style="color:red;">`mkdir`</mark> command to make directories, the <mark style="color:red;">`rmdir`</mark> command to remove directories **(if they are empty)**, and the <mark style="color:red;">`rm`</mark> command to recursively delete directories containing files.

There are 3 types of permissions: **`read`**, **`write`**, and **`execute`**. Each has different meaning depending on whether they are applied to a _file_ or a _directory._

* Read:
  * On a file, this allows processes to **read the contents of the file**, meaning the contents can be viewed and copied.
  * On a directory, **file names in the directory can be listed**, but other details are not available.
* Write:
  * A **file can be written to by the process**, so changes to a file can be saved. Requires the <mark style="color:red;">read</mark> permissions to work correctly
  * On a directory, **files can be added or removed from the directory**. Requires the <mark style="color:red;">execute</mark> permission to work correctly.
* Execute:
  * A **file can be executed** or run as a process.
  * On a directory, **the user can use the **<mark style="color:red;">**`cd`**</mark>** command to "get into" the directory** and use the directory in a path-name to access files.

Typically, there are **two places where you should always have the **_**write**_** and **_**execute**_** permissions** on the directory: **your home directory** (for permanent files) and **`/tmp` directory** (for temporary files).

## Listing Files

While technically not a file management command, the ability to list files using the <mark style="color:red;">`ls`</mark> command is critical to file management. By default, the `ls` command will list the files in the current directory:

```bash
sysadmin@localhost:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos 
```

Note the use of the period `.` character in the following example. When used where a directory is expected, it represents the current directory.

```bash
sysadmin@localhost:~$ ls . /usr/local                                      
.:                                                                   
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos       
                                                                        
/usr/local:                                                              
bin  etc  games  include  lib  man  sbin  share  src
```

**Hidden files** and directories are typically used for individual specific data or settings. Using the <mark style="color:red;">**`-a`**</mark> option will display all files, including hidden files:

```bash
sysadmin@localhost:~$ ls -a
.             .bashrc   .selected_editor  Downloads  Public	 
..            .cache	Desktop       	  Music      Templates
.bash_logout  .profile  Documents     	  Pictures   Videos
```

The `-A` option to the `ls` command will display almost all files of a directory. The current directory file `.` and the parent directory file `..` are omitted.

```bash
sysadmin@localhost:~$ ls -A                                         	 
.bash_logout  .profile     	 Documents  Pictures   Videos
.bashrc       .selected_editor   Downloads  Public                   	 
.cache         Desktop       	 Music      Templates
```

The <mark style="color:red;">`-d`</mark> option to the <mark style="color:red;">`ls`</mark> command displays the directory itself:

```
sysadmin@localhost:~$ ls -ld /var/log                                   
drwxrwxr-x 1 root syslog 4096 Mar  8 02:11 /var/log
```

### File type

```bash
-rw-r--r-- 1 root root	15322 Feb 22 16:32 alternatives.log
drwxr-xr-x 1 root root	 4096 Jul 19  2018 apt
```

The first character of each line indicates the type of file. These types are:

| Symbol | File Type      | Description                                                               |
| ------ | -------------- | ------------------------------------------------------------------------- |
| `d`    | directory      | A file used to store other files.                                         |
| `-`    | regular file   | Includes readable files, image files, binary files, and compressed files. |
| `l`    | symbolic link  | Points to another file.                                                   |
| `s`    | socket         | Allows for communication between processes.                               |
| `p`    | pipe           | Allows for communication between processes.                               |
| `b`    | block file     | Used to communicate with hardware.                                        |
| `c`    | character file | Used to communicate with hardware.                                        |

#### Hard Link Count

```
-rw-r----- 1 syslog adm 560 Mar 8 02:17 auth.log
```

‌⁠​​⁠​ There is only one directory name that links to this file.

#### Timestamp

```
-rw-rw---- 1 root   utmp      0 May 26  2018 btmp
-rw-r--r-- 1 root   adm   85083 Feb 22 16:32 dmesg
```

This indicates the time that the file's contents were last modified. Using the <mark style="color:red;">**`ls`**</mark> command with the <mark style="color:red;">**`--full-time`**</mark> option will display timestamps in full detail.

```bash
sysadmin@localhost:~$ ls -t --full-time                                         
total 32                                                                        
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Desktop
drwxr-xr-x 4 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Documents  
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Downloads  
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Music   
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Pictures
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Public  
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Templates  
drwxr-xr-x 2 sysadmin sysadmin 4096 2019-02-22 16:32:34.000000000 +0000 Videos
```

#### Sorting

The output of the `ls` command is sorted alphabetically by filename. It can sort by other methods as well:&#x20;

| Option                                   | Function                  |
| ---------------------------------------- | ------------------------- |
| <mark style="color:red;">**`-S`**</mark> | Sorts files by size       |
| <mark style="color:red;">**`-t`**</mark> | Sorts by timestamp        |
| <mark style="color:red;">**`-r`**</mark> | Reverses any type of sort |

#### Recursion

When the recursive option <mark style="color:red;">**`-R`**</mark> is used with file management commands, it means to apply that command to not only the specified directory, but also to **all sub-directories and all of the files within all sub-directories.**

```bash
sysadmin@localhost:~$ ls -lR /var/log
/var/log:                                                                       
total 900

-rw-r--r-- 1 root   root  15322 Feb 22 16:32 alternatives.log          	 
drwxr-xr-x 1 root   root   4096 Jul 19  2018 apt                      	 
-rw-r----- 1 syslog adm     560 Mar  8 02:17 auth.log                  	 
-rw-r--r-- 1 root   root  35330 May 26  2018 bootstrap.log             	 
-rw-rw---- 1 root   utmp      0 May 26  2018 btmp                      	 
-rw-r----- 1 syslog adm     293 Mar  8 02:17 cron.log                  	 
-rw-r--r-- 1 root   adm   85083 Feb 22 16:32 dmesg                     	 
-rw-r--r-- 1 root   root 351960 Jul 19  2018 dpkg.log                  	 
-rw-r--r-- 1 root   root  32064 Feb 22 16:32 faillog                   	 
drwxr-xr-x 2 root   root   4096 Jul 19  2018 journal                  	 
-rw-rw-r-- 1 root   utmp 292584 Mar  8 02:11 lastlog                   	 
-rw-r----- 1 syslog adm   14289 Mar  8 02:17 syslog                    	 
-rw------- 1 root   root  64128 Feb 22 16:32 tallylog                  	 
-rw-rw-r-- 1 root   utmp.   384 Mar  8 02:11 wtmp                             
                                                                            
/var/log/apt:                                                                   
total 144                                                                    
-rw-r--r-- 1 root root  13232 Jul 19  2018 eipp.log.xz                      
-rw-r--r-- 1 root root  18509 Jul 19  2018 history.log                      
-rw-r----- 1 root adm  108711 Jul 19  2018 term.log   
```

{% hint style="info" %}
**Consider this**

Some commands use the <mark style="color:red;">**`-r`**</mark> option for recursion while others use the <mark style="color:red;">**`-R`**</mark> option. The **`-R`** option is used recursively with the `ls` command because the <mark style="color:red;">**`-r`**</mark> option is **used for reversing how the files are sorted.**
{% endhint %}

## Viewing File Types

To avoid viewing binary files, use the <mark style="color:red;">**`file`**</mark> command. **The **<mark style="color:red;">**`file`**</mark>** command "looks at" the contents of a file to report what kind of file it is**; it does this by matching the content to known types stored in a _magic file._

{% hint style="info" %}
It is a good idea to use the <mark style="color:red;">**`file`**</mark> command before attempting to access a file to make sure that it does contain text
{% endhint %}

```bash
sysadmin@localhost:~$ file Documents/newhome.txt
Documents/newhome.txt: ASCII text
```

While the <mark style="color:red;">`cat`</mark> command is able to output the contents of binary files, the terminal application may not handle displaying the content of binary files correctly.

```bash
sysadmin@localhost:~$ file /mbin/ls
/bin/ls: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically lin
ked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]
=9567f9a28e66f4d7ec4baf31cfbf68d0410f0ae6, stripped
```

## Creating and Modifying files

The <mark style="color:red;">**`touch`**</mark> command performs two functions. It can **create an empty file or update the modification timestamp on an existing file**.

```
touch NEWFILE
```

If the argument provided is an existing file, then the <mark style="color:red;">**`touch`**</mark> command will update the file's timestamp:

```bash
sysadmin@localhost:~$ ls -l Documents/red.txt
-rw-r--r-- 1 sysadmin sysadmin 51 Feb 22 16:32 Documents/red.txt 
sysadmin@localhost:~$ touch Documents/red.txt
sysadmin@localhost:~$ ls -l Documents/red.txt
-rw-r--r-- 1 sysadmin sysadmin 51 Mar  8 02:59 Documents/red.txt 
```

Each file has 3 timestamps:

* The last time the file's **contents were modified**. This is the timestamp provided by the `ls -l`
* The last time the **file was accessed**. To **modify this timestamp**, use the <mark style="color:red;">**`-a`**</mark> **option** with the <mark style="color:red;">**`touch`**</mark> command
* The last time the **file attributes** (permissions and file ownership) **were modified**. To **modify this timestamp** of file attributes, use the <mark style="color:red;">**`-c`**</mark> **option** with the <mark style="color:red;">**`touch`**</mark> command.

The <mark style="color:red;">`touch`</mark> command will normally update the specified time to the current time, but the <mark style="color:red;">`-t`</mark> option with a timestamp value can be use instead.

```bash
sysadmin@localhost:~$ touch -t 201912251200 newfile
sysadmin@localhost:~$ ls -l newfile
-rw-rw-r-- 1 sysadmin sysadmin 0 Dec 25  2019 newfile
```

In the above command, the date/time syntax for the `touch` command is `CCYYMMDDHHMM`

| Syntax | Example | Meaning     |
| ------ | ------- | ----------- |
| `CC`   | `20`    | The century |
| `YY`   | `19`    | The year    |
| `MM`   | `12`    | The month   |
| `DD`   | `25`    | The day     |
| `HH`   | `12`    | The hour    |
| `MM`   | `00`    | The minutes |

Optionally add `SS` for the seconds.

In order **to view all three timestamps that are kept for a file**, use the <mark style="color:red;">**`stat`**</mark> command with the path to the file.

```bash
 sysadmin@localhost:~$ stat Documents/alpha.txt                                       
  File: Documents/alpha.txt                                                     
  Size: 390             Blocks: 8          IO Block: 4096   regular file        
Device: 802h/2050d      Inode: 5898795     Links: 1                             
Access: (0644/-rw-r--r--)  Uid: ( 1001/sysadmin)   Gid: ( 1001/sysadmin)        
Access: 2019-02-22 16:32:34.000000000 +0000                                     
Modify: 2019-02-22 16:32:34.000000000 +0000                                     
Change: 2019-02-22 16:37:01.149507126 +0000                                     
 Birth: -
```

To create a file with the <mark style="color:red;">**`touch`**</mark> command, you must have the _write_ and _execute_ permission on the parent directory. If the file already exists, modifying it with the <mark style="color:red;">**`touch`**</mark> command only requires the _write_ permission on the file itself.

| Command               | File Type        | Permission     |
| --------------------- | ---------------- | -------------- |
| `touch NEW_FILE`      | Parent Directory | Write, Execute |
| `touch EXISTING_FILE` | File             | Write          |

## Copying Files

The <mark style="color:red;">**`cp`**</mark> command is used to copy files. It takes at least two arguments: the first argument is the path to the file to be copied and the second argument is the path to where the copy will be placed. The files to be copied are sometimes referred to as the source, and the location where the copies are placed is called the destination.

```
cp [OPTION]... SOURCE DESTINATION
```

To copy a file and rename the file in one step you can execute a command like:

```bash
sysadmin@localhost:~$ cp /etc/services ~/ports
```

Using the <mark style="color:red;">**`-v`**</mark> **option** makes the <mark style="color:red;">**`cp`**</mark> command **print verbose output**, so you can always tell what copies succeed as well as those that fail.

**In order to copy a directory**, its contents must be copied as well, including all files within the directories and all of its sub-directories. **This can be done by using the **<mark style="color:red;">**`-r`**</mark>** or **<mark style="color:red;">**`-R`**</mark>** recursive options.**

```bash
sysadmin@localhost:~$ cp -R -v /etc/perl ~                              
'/etc/perl' -> '/home/sysadmin/perl'                                     
'/etc/perl/CPAN' -> '/home/sysadmin/perl/CPAN'                          
'/etc/perl/Net' -> '/home/sysadmin/perl/Net'                             
'/etc/perl/Net/libnet.cfg' -> '/home/sysadmin/perl/Net/libnet.cfg'
```

{% hint style="info" %}
The archive <mark style="color:red;">**`-a`**</mark> **option** of the <mark style="color:red;">**`cp`**</mark> command copies the contents of the file and also attempts to **maintain the original timestamps and file ownership**.&#x20;

The <mark style="color:red;">**`-a`**</mark> **option** also **implies that recursion will be done**. Therefore, it can also be used to copy a directory.
{% endhint %}

## Moving Files and Directories

The <mark style="color:red;">**`mv`**</mark> command is used to move a file from one path name in the filesystem to another. When you move a file, **it's like creating a copy of the original file** with a new path name and then deleting the original file.

There is no need for a recursive option with the <mark style="color:red;">**`mv`**</mark> command. **When a directory is moved, everything it contains is automatically moved as well.**

Moving a file within the same directory is an effective way to rename it

## Creating Directories

The <mark style="color:red;">**`mkdir`**</mark> command allows you to create (make) a directory. Creating directories is an essential file management skill, since you will want to maintain some functional organization with your files and not have them all placed in a single directory.

By adding the <mark style="color:red;">**`-p`**</mark> **option**, the <mark style="color:red;">**`mkdir`**</mark> command automatically **creates the **_**parent directories**_** for any child directories about to be created**. This is especially useful for making deep path names.

```bash
sysadmin@localhost:~$ mkdir -p /home/sysadmin/red/blue/yellow/green
sysadmin@localhost:~$ ls -R red
red:
blue
 
red/blue:
yellow
 
red/blue/yellow:
green
 
red/blue/yellow/green:
```

To create a directory with the **`mkdir`** command, you must have the write and execute permissions on the parent of the proposed directory.&#x20;

## Removing Directories

The <mark style="color:red;">**`rmdir`**</mark> command is used to remove empty directories

```
rmdir [OPTION]... DIRECTORY...
```

```
sysadmin@localhost:~$ rmdir bin
```

Using the <mark style="color:red;">**`-p`**</mark> **option** with the <mark style="color:red;">**`rmdir`**</mark> command **will remove directory paths, but only if all of the directories contain other empty directories**.

```bash
sysadmin@localhost:~$ rmdir red
rmdir: failed to remove 'red': Directory not empty
sysadmin@localhost:~$ rmdir -p red/blue/yellow/green
```

Otherwise, if a directory contains anything expect ohter directories, you'll need to use the <mark style="color:red;">**`rm`**</mark> command with a recursive option. The <mark style="color:red;">**`rm`**</mark> command alone will ignore directories that it's asked to remove; to delete a directory, use either the <mark style="color:red;">`-r`</mark> or <mark style="color:red;">`-R`</mark> recursive options. Just be careful as this will delete all files and all subdirectories

```bash
sysadmin@localhost:~$ mkdir test
sysadmin@localhost:~$ touch test/file1.txt 
sysadmin@localhost:~$ rmdir test
rmdir: failed to remove 'test': Directory not empty
sysadmin@localhost:~$ rm test                                 
rm: cannot remove 'test': Is a directory     
sysadmin@localhost:~$ rm -r test
sysadmin@localhost:~$
```
