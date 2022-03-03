# Chapter 5: File Manipulation

Everything is considered a file in Linux. Even directories are considered a files since they are special files that are used to contain other files.

There are 3 types of permissions: read, write, and execute. Each has different meaning depending on whether they are applied to a _file_ or a _directory._

* Read:
  * On a file, this allows processes to read the contents of the file, meaning the contents can be viewed and copied.
  * On a directory, file names in the directory can be listed, but other details are not available.
* Write:
  * A file can be written to by the process, so changes to a file can be saved. **Requires the **<mark style="color:red;">**read**</mark>** permissions to work correctly**
  * On a directory, files can be added or removed from the directory. **Requires the **<mark style="color:red;">**execute**</mark>** permission to work correctly.**
* Execute:
  * A file can be executed or run as a process.
  * On a directory, the user can use the `cd` command to "get into" the directory and use the directory in a path-name to access files.

Typically, there are two places where you should always have the _write_ and _execute_ permissions on the directory: your home directory (for permanent files) and `/tmp` directory (for temporary files).

## Listing Files

The <mark style="color:red;">`-d`</mark> option to the <mark style="color:red;">`ls`</mark> command displays the directory itself

```
ls -ld Documents/
```

### File type

```
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

#### Timestamp

```
-rw-rw---- 1 root   utmp      0 May 26  2018 btmp
-rw-r--r-- 1 root   adm   85083 Feb 22 16:32 dmesg
```

This indicates the time that the file's contents were last modified. Using the `ls` command with the <mark style="color:red;">`--full-time`</mark> option will display timestamps in full detail.

#### Sorting

The output of the `ls` command is sorted alphabetically by filename. It can sort by other methods as well:&#x20;

| Option | Function                  |
| ------ | ------------------------- |
| `-S`   | Sorts files by size       |
| `-t`   | Sorts by timestamp        |
| `-r`   | Reverses any type of sort |

#### Recursion

When the recursive option is used with file management commands, it means to apply that command to not only the specified directory, but also to all sub-directories and all of the files within all sub-directories.

```
ls -lR /var/log
```

## Creating and Modifying files

The touch command performs two functions. It can create an empty file or update the modification timestamp on an existing file.

```
touch NEWFILE
```

Each file has 3 timestamps:

* The last time the file's contents were modified. This is the timestamp provided by the `ls -l`
* The last time the file was accessed. To modify this timestamp, use the <mark style="color:red;">`-a`</mark> option with the <mark style="color:red;">`touch`</mark> command
* The last time the file attributes(permissions and file ownership) were modified. To modify this timestamp, use the <mark style="color:red;">`-c`</mark> option with the <mark style="color:red;">`touch`</mark> command.

The <mark style="color:red;">`touch`</mark> command will normally update the specified time to the current time, but the <mark style="color:red;">`-t`</mark> option with a timestamp value can be use instead.

```
 touch -t 201912251200 newfile
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

Optionally add SS for the seconds.

In order to view all three timestamps that are kept for a file, use the <mark style="color:red;">`stat`</mark> command with the path to the file.

```
 stat Documents/alpha.txt                                       
  File: Documents/alpha.txt                                                     
  Size: 390             Blocks: 8          IO Block: 4096   regular file        
Device: 802h/2050d      Inode: 5898795     Links: 1                             
Access: (0644/-rw-r--r--)  Uid: ( 1001/sysadmin)   Gid: ( 1001/sysadmin)        
Access: 2019-02-22 16:32:34.000000000 +0000                                     
Modify: 2019-02-22 16:32:34.000000000 +0000                                     
Change: 2019-02-22 16:37:01.149507126 +0000                                     
 Birth: -
```

## Copying Files

The <mark style="color:red;">`cp`</mark> command is used to copy files.

Using the <mark style="color:red;">`-v`</mark> option makes the `cp` command print verbose output, so you can always tell what copies succeed as well as those that fail.

In order to copy a directory, its contents must be copied as well, including all files within the directories and all of its sub-directories. This can be done by using the <mark style="color:red;">`-r`</mark> or <mark style="color:red;">`-R`</mark> recursive options.

{% hint style="info" %}
The archive <mark style="color:red;">`-a`</mark> option of the <mark style="color:red;">`cp`</mark> command copies the contents of the file and also attempts to **maintain the original timestamps and file ownership**.&#x20;

The <mark style="color:red;">`-a`</mark> option also implies that recursion will be done. Therefore, it can also be used to copy a directory.
{% endhint %}

## Creating Directories

The `mkdir` command allows you to create (make) a directory.

By adding the <mark style="color:red;">`-p`</mark> option, the `mkdir` command automatically creates the _parent directories_ for any child directories about to be created. This is especially useful for making deep path names.

