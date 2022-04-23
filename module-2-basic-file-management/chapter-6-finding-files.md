---
description: >-
  103.3: Perform basic file management v2  Weight:
  4                                                                       
  104.7: Find system files and place files in the correct location v2  Weight: 2
---

# Chapter 6: Finding Files

<details>

<summary>Key terms</summary>

`/etc/updatedb.conf` A configuration file for the updatedb utility

`find` Command used to search for files in a directory hierarchy. find searches the directory tree rooted at each given file name by evaluating the given expression from left to right.

`locate` Command uses to search for files by name. locate reads one or more databases prepared by the updatedb utility and writes file names matching at least one of the PATTERNs to standard output.

`type` Command that indicates how a name would be interpreted if used as command

`updatedb` The updatedb utility creates or update a database to be used by the locate utility.

`whereis` Command that is used to locate source/binary and manuals sections for specified rules. This will locate binary, source, and manual pages for a command.

`which` Command that returns the pathnames of the files, or links, which would be executed in the current environment. It does this by searching the PATH for executables matching the names of the arguments

</details>

## Filesystem Hierarchy Standard (FHS)

The FHS is an agreement to **standardize the names and locations of directories and their content** for use within most Linux filesystems. t helps to know what directories to expect to find, and what files one should expect to find in them. More importantly, it allows programmers to write programs that will be able to work across a wide variety of systems that conform to this standard.

| Directory           | Purpose                                                        |
| ------------------- | -------------------------------------------------------------- |
| `/`                 | The root of the primary filesystem hierarchy.                  |
| `/bin`              | Contains essential user binary executable.                     |
| `/boot`             | Contains the kernel and boot-loader files.                     |
| `/dev`              | Populated with files representing attached devices.            |
| `/etc`              | Configuration files specific to the host.                      |
| `/home`             | Common location for user home directories.                     |
| `/lib`              | Essential libraries to support `/bin` and `/sbin` executable.  |
| `/mnt`              | Mount point for temporarily mounting a filesystem.             |
| `/opt`              | Optional third-party add-on software.                          |
| `/root`             | Home directory for the root user.                              |
| `/sbin`             | Contains system or administrative binary executable.           |
| `/srv`              | May contain data provided by system services.                  |
| `/tmp`              | Location for creating temporary files.                         |
| `/usr`              | The root of the secondary filesystem hierarchy.                |
| `/usr/bin`          | Contains the majority of the user commands.                    |
| `/usr/include`      | Header files for compiling C-based software.                   |
| `/usr/lib`          | Shared libraries to support `/usr/bin` and `/usr/sbin`.        |
| `/usr/local`        | The root of the third filesystem hierarchy for local software. |
| `/usr/sbin`         | Non-vital system or administrative executable.                 |
| `/usr/share`        | Location for architecturally-independent data files.           |
| `/usr/share/dict`   | Word lists.                                                    |
| `/usr/share/doc`    | Documentation for software packages.                           |
| `/usr/share/info`   | Information pages for software packages.                       |
| `/usr/share/locale` | Locale information.                                            |
| `/usr/share/man`    | Location for man pages.                                        |
| `/usr/share/nls`    | Native language support files.                                 |

Vendors of Linux distributions have continued to make some changes, even though a new version of the standard has not been published in over ten years. Two notable new additions include the `/run` directory and the `/sys` directory.&#x20;

The **`/run`** directory is being considered for use in the forthcoming FHS versions to **contain volatile data that changes at runtime**. Previously, this data was supposed to be stored under the `/var/run` directory, but due to the unavailability of this directory at boot time, this data can become scattered in other places, such as hidden files in the `/dev` directory.

The **`/sys`** directory in some traditional UNIX systems was used to hold files related to the kernel. In modern Linux systems, the **`/sys`** directory is **used to mount the `sysfs` pseudo-filesystem**. This filesystem is used to export information about kernel objects and their relationships to each other.

Another notable change that some Linux distributions are making is the conversion of the `/bin`, `/sbin` and `/lib` directories into symbolic links which point to `/usr/bin`, `/usr/sbin` and `/usr/lib`, respectively. All user executables are now in the `/usr/bin` directory, administrator executables are now in the `/usr/sbin` directory, and the libraries to support all these executables are now in the `/usr/lib` directory.

Because of the way that UNIX booted, the `/bin`, `/sbin` and `/lib` directories had to be part of the root filesystem as they contain critical boot executables. Some developers now argue that the reason for having them split is no longer valid. In early versions of UNIX, the developers had two filesystems of about 1.5 MiB each on two separate disks for the root filesystem and the `/usr` filesystem. As the root filesystem started to become full, the developers decided to move some of the executable files that were in the `/bin` and `/sbin` directories that were not essential to booting the system into the corresponding directories `/usr/bin` and `/usr/sbin` (in the separate `/usr` filesystem).

The **FHS** standard categorizes each system directory in a couple of ways for security purposes:

### Shareable/Un-shareable

* _**Shareable**_ files can be **stored on one host and used on others**. For instance, `/var/www` is often used as the root directory of a web server, which shares files with other hosts.
* _**Un-shareable**_ files **should not be shared between hosts**. These includes process states in the `/var/run` directory and the `/boot` directory

### Variable/Static

* **Static files generally do not change**, including library files and documentation pages. An example is the info pages located at /usr/share/info
* **Variable files normally change** during the execution of programs.

|              | Shareable                                                        | Unshareable                                               |
| ------------ | ---------------------------------------------------------------- | --------------------------------------------------------- |
| **Static**   | <p><code>/usr</code></p><p><code>/opt</code></p>                 | <p><code>/etc</code></p><p><code>/boot</code></p>         |
| **Variable** | <p><code>/var/mail</code></p><p><code>/var/spool/news</code></p> | <p><code>/var/run</code></p><p><code>/var/lock</code></p> |

## Finding Files and Commands

Both the <mark style="color:red;">**`locate`**</mark> and <mark style="color:red;">**`find`**</mark> commands are useful for searching for a file within the filesystem.

### `locate` Command

The <mark style="color:red;">`locate`</mark> command it's **fast but** **out-of-date**. Its speed comes from the fact that the <mark style="color:red;">`locate`</mark> command **searches a database that contains the location of the files** on the filesystem, but that **database needs to be updated to be accurate.**

The <mark style="color:red;">`locate`</mark> command accepts a search string as an argument.

```bash
sysadmin@localhost:~$ locate passwd                                     
/etc/passwd                                                         
/etc/passwd-                                                           
/etc/pam.d/chpasswd                                          
/etc/pam.d/passwd                                                
/etc/security/opasswd                                             
/usr/bin/gpasswd                                                     
/usr/bin/passwd                                                   
/usr/lib/tmpfiles.d/passwd.conf  
```

A few thing to consider:

* Only **return results** of files that the current **user would normally have access to**.
* The <mark style="color:red;">**`locate`**</mark> command **will** **display all files** that have the **search term anywhere in the filename**.
* The <mark style="color:red;">**`locate`**</mark> command is **case sensitive**. To have the locate command not be case sensitive, use the <mark style="color:red;">**`-i`**</mark> option.

{% hint style="info" %}
The <mark style="color:red;">`locate`</mark> command is **dependent on a database**. This database can be updated manually by an admin using the <mark style="color:red;">`updatedb`</mark> command.

The <mark style="color:red;">**`locate`**</mark> command accepts the <mark style="color:red;">**`-r`**</mark> option to use regular expressions in the search pattern which provides a more powerful way to search for files.
{% endhint %}

When executed, the <mark style="color:red;">`updatedb`</mark> command creates a database of all files that it finds on the computer for quick searching. This command can only be executed by a user with administrative access, which can be achieved using the <mark style="color:red;">`sudo`</mark> command:

The <mark style="color:red;">`updatedb`</mark> command can be told not to search a particular name, path, or filesystem by changing the corresponding line in its configuration file, `/etc/updatedb.conf`. Below is an example of the default file in its entirety:

```bash
cat /etc/updatedb.conf                                  
PRUNE_BIND_MOUNTS="yes"                                                         
# PRUNENAMES=".git .bzr .hg .svn"                                               
PRUNEPATHS="/tmp /var/spool /media /var/lib/os-prober /var/lib/ceph /home/.ecryp
tfs /var/lib/schroot"                                               

PRUNEFS="NFS nfs nfs4 rpc_pipefs afs binfmt_misc proc smbfs autofs iso9660 ncpfs
 coda devpts ftpfs devfs devtmpfs fuse.mfs shfs sysfs cifs lustre tmpfs usbfs ud
f fuse.glusterfs fuse.sshfs curlftpfs ceph fuse.ceph fuse.rozofs ecryptfs fusesm
b"
```

However, if you want to use the <mark style="color:red;">**`locate`**</mark> command to search for a file that was created very recently, it will fail to find the file if the database hasn't been updated since the file creation

### `find` Command

The <mark style="color:red;">**`find`**</mark> command is **slower** t**han the **<mark style="color:red;">**`locate`**</mark>** command** because it **searches directories in real time**; however ,it doesn't suffer from problems associated with an outdated database.

{% hint style="warning" %}
The <mark style="color:red;">`find`</mark> command **expects a directory as the first argument** and will search this directory and all of its sub-directories. If no directory is specified, then the <mark style="color:red;">`find`</mark> command will start the search at the current directory.
{% endhint %}

```
find [OPTIONS]...[starting point] [expression]
```

Note that the period `.` character refers to the current directory in the following example, but the current directory could also be referred to using the `./` notation. The <mark style="color:red;">`find`</mark> command uses the <mark style="color:red;">**`-name`**</mark> option to search for files by name, in this case, the `Downloads` directory.

```bash
sysadmin@localhost:~$ find . -name Download
sysadmin@localhost:~$ find . -name Downloads
./Downloads
sysadmin@localhost:~$ find . -name Dow*
./Downloads
sysadmin@localhost:~$ find . -name 'Do*'
./Documents
./Downloads
```

{% hint style="danger" %}
**The string must match the exact name of the file**, not just the part of the name. Globbing can be used.
{% endhint %}

The <mark style="color:red;">`find`</mark> command also offers many options for searching files, unlike the locate command which searches only for files based on file name.

| Example           | Meaning                                                                          |
| ----------------- | -------------------------------------------------------------------------------- |
| `-iname LOSTFILE` | Case insensitive search by name.                                                 |
| `-mtime -3`       | Files modified less than three days ago.                                         |
| `-mmin -10`       | Files modified less than ten minutes ago.                                        |
| `-size +1M`       | Files larger than one megabyte.                                                  |
| `-user joe`       | Files owned by the user joe.                                                     |
| `-nouser`         | Files not owned by any user.                                                     |
| `-empty`          | Files that are empty.                                                            |
| `-type d`         | Files that are directory files.                                                  |
| `-maxdepth 1`     | Do not use recursion to enter subdirectories; only search the primary directory. |

If multiple criteria are specified, the all criteria must match as the <mark style="color:red;">**`find`**</mark>** command automatically assumes a logical **_**AND**_** condition between criteria**, meaning all of the conditions must be met. This could be explicitly stated by using the <mark style="color:red;">**`-a`**</mark> option between criteria. For example, the `Downloads` directory must also be owned by the `sysadmin` user in order for the <mark style="color:red;">`find`</mark> command to produce a result in the following example:

```bash
sysadmin@localhost:~$ find . -user sysadmin -a -name Downloads
./Downloads
```

**Logical **_**OR**_** conditions can be specified between criteria** with the <mark style="color:red;">**`-o`**</mark> option, meaning at least one of the conditions must be true.

```bash
sysadmin@localhost:~$ find . -user sysadmin -o -name Downloads
.                                                                
./Public                                                          
./Downloads                                                        
./Pictures                                                    
./Videos                                                          
./.selected_editor                                                 
./.profile                                                   
 ./.bash_logout
```

Logical groupings can also be specified using parenthesis

```bash
#Return files in the current directory, with the non-case sensitive name starting
#with desk OR files with the exact name Downloads owned by the user jdoe

find . -iname 'desk*' -o \( -name Downloads -a -user jdoe \)
```

By default, the <mark style="color:red;">`find`</mark> command simply prints the names of the files that it finds. To generate outputs showing additional file details, similar to the <mark style="color:red;">`ls -l`</mark> command, you can specify the <mark style="color:red;">`-ls`</mark> options

```bash
find . -ls -iname 'desk*' -o \(-name Downloads -a -user jdoe \)
209950600      4 drwxr-xr-x   1 sysadmin sysadmin     4096 Mar 10 04:08 .       
210019331      4 drwxr-xr-x   2 sysadmin sysadmin     4096 Mar  8 19:10 ./Templates                                                                             
102108986      4 -rw-r--r--   1 sysadmin sysadmin      220 Apr  4  2018 ./.bash_logout                                                                          
102108988      4 -rw-r--r--   1 sysadmin sysadmin      807 Apr  4  2018 ./.profile                                                                              
210019332      4 drwxr-xr-x   2 sysadmin sysadmin     4096 Mar  8 19:10 ./Downloads  
```

To make the output exactly like the the output of the <mark style="color:red;">`ls -l`</mark> command, use the <mark style="color:red;">**`-exec`**</mark> option to execute <mark style="color:red;">**`ls -l`**</mark> on each file found

```bash
sysadmin@localhost:~$ find -name 'Documents' -exec ls -l {} \;                       
total 1100                                                                      
drwxr-xr-x 5 sysadmin sysadmin   4096 Mar  8 19:10 School                       
drwxr-xr-x 2 sysadmin sysadmin   4096 Mar  8 19:10 Work                         
-rw-r--r-- 1 sysadmin sysadmin     39 Mar  8 19:10 adjectives.txt               
-rw-r--r-- 1 sysadmin sysadmin     90 Mar  8 19:10 alpha-first.txt              
-rw-r--r-- 1 sysadmin sysadmin    106 Mar  8 19:10 alpha-second.txt             
-rw-r--r-- 1 sysadmin sysadmin    195 Mar  8 19:10 alpha-third.txt
```

```bash
#Tells the find command to execute the ls -l command for each file found. The pair
# of curly braces {} is a placeholder for the name of each file found. The \; is
#an escaped semicolon that is added between each command so that multiple commands
#may be executed in series
find -name 'Documents' -exec ls -l {} \;
find -name 'user.txt' -exec ls -l {} \;
```

In order to make the <mark style="color:red;">`find`</mark> command **confirm the execution of the command for each file found**, use the action option <mark style="color:red;">`-ok`</mark> instead of the <mark style="color:red;">`-exec`</mark> option

```bash
find . -mmin -2 -ok rm {} \;
< rm ... . > ? n
< rm ... ./one > ? y
< rm ... ./two > ? y
< rm ... ./three > ? y
```

The modification time <mark style="color:red;">`-mtime`</mark> option to the <mark style="color:red;">`find`</mark> command provides the ability to give time as criteria for a search.

| Option      | Function                                    |
| ----------- | ------------------------------------------- |
| `-mtime N`  | Files modified `N`\*24 hours ago.           |
| `-mtime -N` | Files modified less than `N`\*24 hours ago. |
| `-mtime +N` | Files modified more than `N`\*24 hours ago. |

* To summarize, a number without a `+` or  `-` **means exactly **_**N**_** days ago.**
* A number with a `-` means anytime between _N_ days ago and NOW.
* A number with a `+` means N days ago or more.

The <mark style="color:red;">`find`</mark> command is less exact by default than you might want it to be. When using numbers of days with the <mark style="color:red;">`-atime`</mark>, <mark style="color:red;">`-ctime`</mark> or <mark style="color:red;">`-mtime`</mark> operators,  the days refer to 24-hour blocks.

To find all files that are newer, you can use it with the <mark style="color:red;">`find -newer`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command.&#x20;

The find command also allows the use of friendly notation to find files that are larger or smaller than a particular size.

```bash
#Find all files that are smaller than 1 Kilobyte in the /etc directory
find /etc -size -1k

#Find all files that are 1 Megabyte or more bigger
find /etc -size +1M
```

The <mark style="color:red;">`find`</mark> command can also help you **find files by specifying a file type**, such as regular file, directory, and more, using the <mark style="color:red;">**`-type`**</mark> option.&#x20;

| Symbol  | File Type                                                |
| ------- | -------------------------------------------------------- |
| `b`     | Block (i.e., disks, storage)                             |
| `c`     | Character (i.e., keyboards, scanners, mice)              |
| **`d`** | Directory (directory files)                              |
| `p`     | Named pipes (Allows for communication between processes) |
| **`f`** | Regular file (i.e., scripts, text files, graphics)       |
| `s`     | Sockets (Allows for communication between processes)     |

To demonstrate, in order to search only for directories under the `/home` directory, you could use the following command:

```bash
sysadmin@localhost:~$ find /home/sysadmin -type d               
/home/sysadmin                                            
/home/sysadmin/Desktop                                   
/home/sysadmin/Templates                          
/home/sysadmin/Documents                 
/home/sysadmin/Documents/Work        
/home/sysadmin/Documents/School    
/home/sysadmin/Documents/School/Engineering
/home/sysadmin/Documents/School/Art       
/home/sysadmin/Documents/School/Math                
/home/sysadmin/Music                                        
/home/sysadmin/Public
```

{% hint style="info" %}
To **limit the search to just a single level** below the `/home` directory, the <mark style="color:red;">**`-maxdepth`**</mark> option to the <mark style="color:red;">`find`</mark> command can be used.

```bash
sysadmin@localhost:~$ find /home/sysadmin -type d -maxdepth 1
```
{% endhint %}

{% hint style="warning" %}
The <mark style="color:red;">`find`</mark> command generates a lot of output related to files that it cannot access due to inadequate permissions. **To suppress this type of output, redirect it to `/dev/null`**, as shown in the example below. To find the `hostname` file in the `/etc` directory and also redirect the error stream, execute the following command:

```bash
find /etc -name hostname 2>/dev/null
```
{% endhint %}

To display a count of all of the files in the `/etc` directory structure that end in `.conf`, execute the following command:

```
find /etc -name "*.conf" 2>/dev/null | wc -l
92
```

### `whereis` Command

The <mark style="color:red;">`whereis`</mark> command can be **used to search your PATH for the binary**. It is also capable of searching for tha man page, and source files for any given command.

```
whereis [OPTION]...NAME....
```

```bash
sysadmin@localhost:~$ whereis grep
grep: /bin/grep /usr/share/man/man1/grep.1.gz /usr/share/info/grep.info.gz
```

The output of the <mark style="color:red;">`whereis`</mark> command returns three directories.&#x20;

* The first is where the grep command is located.
* The second is where the man page of the grep command is located
* The third is where the info page of the grep command is located.

To view the location of the **binary separate from the man page and info page**, use the <mark style="color:red;">`-b`</mark> and <mark style="color:red;">`-m`</mark> option respectively.

```bash
sysadmin@localhost:~$ whereis -b grep
grep: /bin/grep
sysadmin@localhost:~$ whereis -m grep
grep: /usr/share/man/man1/grep.1.gz /usr/share/info/grep.info.gz
```

The <mark style="color:red;">**`-s`**</mark> option can be used to **find the source code that has been installed** for a given command.&#x20;

The <mark style="color:red;">`-u`</mark> option can be used in one of two ways. It can **identify commands that do not have documentation** (i.e. a man page) for a requested attribute. It can also be used to **identify commands that have more than one documentation file.**

For example, to find out which commands in the `/bin` directory either do not have a man page, or have more than one documentation file, use the following command:

```bash
#The asterisk * character is used to consider every file in the current directory
sysadmin@localhost:/bin$ whereis -m -u *
```

**To limit the search to a specific path or paths, capitalized options can be used**. The <mark style="color:red;">`-B`</mark> option searches for **binaries**, the <mark style="color:red;">`-M`</mark> option for **manuals** and documentation, and the <mark style="color:red;">`-S`</mark> option for **sources**. The `-f` option must be used to indicate the end of the path list and the beginning of the search term.

### `which` Command

Sometimes the <mark style="color:red;">`whereis`</mark> command returns more than one result on a command. In this case, an administrator would want to know which command is being used.

```
which [-a] FILENAME..
```

The `bash` command is an example that normally yields two results:

```bash
sysadmin@localhost:~$ whereis -b bash
bash: /bin/bash /etc/bash.bashrc
```

\
**To find out which **<mark style="color:red;">**`bash`**</mark>** result is the real command**, use the <mark style="color:red;">**`which`**</mark> command:

```bash
sysadmin@localhost:~$ which bash
/bin/bash
```

The <mark style="color:red;">**`-a`**</mark> option can be used with the <mark style="color:red;">`which`</mark> command to **locate multiple executable files**.

> This would be useful to know if an executable script was inserted maliciously to override an existing command

```bash
sysadmin@localhost:~$ which -a ping
/bin/ping
```

By using the <mark style="color:red;">`which`</mark> command, an administrator can be fairly certain that the only executable running by the name of the `ping` command is located in the `/bin` directory.

### `type` Command

The <mark style="color:red;">`type`</mark> command can be used to determine information about various commands

```
type [OPTIONS] ...NAME...
```

Some command originates from a specific file

```
sysadmin@localhost:~$ type which                                                
which is /usr/bin/which
```

The <mark style="color:red;">`type`</mark> command can also identify commands that are built into the Bash (or other) shell:

```
sysadmin@localhost:~$ type echo
echo is a shell builtin
```

Using the <mark style="color:red;">`-a`</mark> option, the <mark style="color:red;">`type`</mark> command can also **reveal the path of another command**:

```bash
sysadmin@localhost:~$ type -a echo
echo is a shell builtin
echo is /bin/echo
```

The <mark style="color:red;">`type`</mark> command supports other options, and **can lookup multiple commands simultaneously**. To **display only a single word describing** the `echo`, `ll`, and `which` commands, use the <mark style="color:red;">`-t`</mark> option:

```bash
sysadmin@localhost:~$ type -t echo ll which
builtin
alias
file
```
