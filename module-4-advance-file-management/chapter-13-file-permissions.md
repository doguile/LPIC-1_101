# Chapter 13: File permissions

When listing a file with the <mark style="color:red;">`ls -l`</mark> command, the output includes permission and ownership information:&#x20;

```
sysadmin@localhost:~$ ls -l /bin/ls
-rwxr-xr-x 1 root root 133792 Jan 18  2018 /bin/ls
```

## File Type Field

The first character of this output indicates the type of a file. Recall if the first character is a dash `-` character, this is a regular file. If the character was a letter `d` ,it would be a directory.

If the first character of the output of the `ls -l` command is a letter `l` ,then the file is a _symbolic link_. A symbolic link file "points to" another file in the file system.

{% hint style="info" %}
To access a symbolic link you need to have the appropiate permissions on both the symbolic link file and the file that it points to.
{% endhint %}

#### Permissions Field

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

After the file type character, the permissions are displayed.

#### User Owner Field

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

After the link count, the file's user owner is displayed.

#### Group Owner Field

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

After the user owner field, the file group owner is displayed.

## File Ownership

By default, users own the files that they create. **While this ownership can be changed, this function **<mark style="color:red;">**requires administrative privileges**</mark>**.**

Every file also has a group owner. <mark style="color:red;">**Users are allowed to change the group owner of files they own to any group that they belong to.  (NOT NECESSARY ROOT PRIVILEGES)**</mark>

The <mark style="color:red;">`id`</mark> command can be useful for verifying which user account you are using and which groups you have available to use.&#x20;

The output of the <mark style="color:red;">`id`</mark> command displays the **UID** and user account name of the current user followed by the **GID** and group name of the primary group and the **GIDs** and group names of all group memberships:

```bash
sysadmin@localhost:~/Documents$ id
uid=1001(sysadmin) gid=1001(sysadmin) groups=1001(sysadmin),4(adm),27(sudo)
```

{% hint style="info" %}
By viewing the output of this command, you can see the user's identity information expressed both as a number and as a name.
{% endhint %}

### Switching Users

The <mark style="color:red;">**`su`**</mark> command **allows you to run a shell as a different user**. While switching to the root user is what the <mark style="color:red;">`su`</mark> command is used for most frequently, it can also <mark style="color:red;">**switch to other users as well.**</mark>

```
su [OPTIONS] [USERNAME]
```

When switching users, **utilizing the login shell options is recommended** as the login shell fully configures the new shell with the settings of the new user, ensuring any commands executed run correctly.  The login shell option can be specified one of three ways

```
su -
su -l
su --login
```

By default, if a username is not specified, the <mark style="color:red;">`su`</mark> command opens a new shell as the root user. The following two commands are equivalent ways to start a shell as the root user

```
su - root
su -
```

> The user most provide the password of the root user to start the new shell.

```bash
sysadmin@localhost:~$ su -
Password:
root@localhost:~# id
uid=0(root) gid=0(root) groups=0(root)
```

The <mark style="color:red;">**`sudo`**</mark> command <mark style="background-color:red;">**allows users to execute commands as another user**</mark>

```
sudo [OPTIONS] COMMAND
```

> When using the <mark style="color:red;">`sudo`</mark> command to execute a command as the root user, the command prompts for the user's own password, not that of the root user.&#x20;

### Changing File User Owner

Initially, the owner of a file is the user who creates it. **The user owner can only be changed by a user with root privileges** using the <mark style="color:red;">`chown`</mark> commnad.&#x20;

```
chown [OPTION]... [OWNER] FILE...
```

```bash
sysadmin@localhost:~/Documents$ ls -l nullfile
-rw-rw-r-- 1 sysadmin sysadmin 0 Mar 21 17:30 nullfile
```

Next, the <mark style="color:red;">`sudo`</mark> command can be used with the `chown` command to change the ownership of the new file to the `root` user, and the `ls -l` command is used to verify the change:

```bash
sysadmin@localhost:~/Documents$ sudo chown root
nullfile
[sudo] password for sysadmin:
sysadmin@localhost:~/Documents$ ls -l nullfile
-rw-rw-r-- 1 root sysadmin 0 Mar 21 17:30 nullfile
```

### Switching Groups

When a user creates a file or directory, their primary group will normally be the group owner. **If you are interested in knowing what groups you belong to , then you can execute the **<mark style="color:red;">**`groups`**</mark> **command**:

```
sysadmin@localhost:~$ groups
sysadmin adm sudo
```

To create a file or directory that will be owned by a group different from your current primary group, one option is to **change** your current primary group to **another group you belong** to by using the <mark style="color:red;">`newgrp`</mark>command.

```
newgrp [GROUP]
```

For example, to change the current primary group from `sysadmin` to the secondary group called `adm` ,execute:

```bash
sysadmin@localhost:~/Documents$ newgrp adm

sysadmin@localhost:~$ touch newfile.txt

sysadmin@localhost:~$ ls -l newfile.txt                                         
-rw-rw-r-- 1 sysadmin adm 0 Mar 21 20:00 newfile.txt

sysadmin@localhost:~$ exit
exit

sysadmin@localhost:~/Documents$
```

{% hint style="info" %}
The <mark style="color:red;">`newgrp`</mark> command opens a new shell and assigns the primary group for that shell to the specified group. Once placed in the new shell, any file created will belong to the new primary group.

To go back to the default primary group, use the `exi`t command to close the new shell that was started by the <mark style="color:red;">`newgrp`</mark> command.
{% endhint %}

{% hint style="danger" %}
**You must be a member of the group that you want to change to.** Additionally, administrators can password protect groups.
{% endhint %}

### Changing File Group Owner

Another option is for the user owner of the file to change the group owner by using the <mark style="color:red;">`chgrp`</mark> command. You can execute the following:

```bash
sysadmin@localhost:~/Documents$ touch newfile2.txt

sysadmin@localhost:~/Documents$ ls -l newfile2.txt
-rw-rw-r-- 1 sysadmin sysadmin 0 Mar 21 18:03 newfile2.txt

sysadmin@localhost:~/Documents$ sudo chgrp adm
newfile2.txt

sysadmin@localhost:~/Documents$ ls -l newfile2.txt
-rw-rw-r-- 1 sysadmin adm 0 Mar 21 18:03 newfile2.txt

sysadmin@localhost:~/Documents$ cd
sysadmin@localhost:~$
```

The <mark style="color:red;">`chgrp`</mark> command does not change your current primary group, so when creating many files, it is more efficently just to use the <mark style="color:red;">`newgrp`</mark> command instead of executing the <mark style="color:red;">`chgrp`</mark> command for each file.

{% hint style="warning" %}
**Only the owner of the file and the root user can change the group ownership of a file.**
{% endhint %}

### Understanding Permissions

User and group owner information are important considerations when determining what permissions will be effective for a particular user. To determine which set applies to a particular user, first examine the user's identity. By using the <mark style="color:red;">`whoami`</mark> command or the <mark style="color:red;">`id`</mark> command, you can display your user identity.

#### Permission Groups

When viewing the long listing of a file, the permissions are broken into three sets of three characters:

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

**User Owner**

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

Characters 2-4 indicate the permissions for the user that owns the file. If you are the owner of the file, then only the user owner permissions are used to determine access to that file.

**Group Owner**

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

Characters 5-7 indicate the permissions for the group that owns the file. If you are not the owner but are member of the group that owns the file, then only group owners permissions are used to determine access to that file.

**Other Permissions**

```
-rwxr-xr-x. 1 root root 133792 Jan  18  2018 /bin/ls
```

Characters 8-10 indicate the permissions for others or what is sometimes referred to as the world's permissions. This group includes all users who are not the file owner or a member of the file's group.

### Permissions Types

Each permission group is granted three basic types of permissions: **read, write and execute**.

The permission themselves are deceptively simple and have a different meaning depending on whether they are applied to a file or a directory.

#### Read

There is a `r` character if the group has the read permission, or a `-` character if the group does not.

* On a file, this allows processes to read the contents of the file, meaning the contents can be viewed and copied.
* On a directory, files names in the directory can be listed, but other details are not available.

#### Write

The second character of each group represents the _write_ permissions. There is a `w` character if the group has the write permissions, or a `-` character if the group does not.

* A file can be written to by the process, so changes to a file can be saved. Note that `w` permission really requires `r` permission on the file to work correctly.
* On a directory, files can be added or removed from the directory. Note that `w` permission requires `r` permission on the directory to work correctly

#### Execute

The third character of each group represents the execute permission. There is a `x` character if the group has the execute permission.

* A file can be executed or run as a process.
* On a directory, the **user can use the **<mark style="color:red;">**`cd`**</mark>** command to "get into" the directory** and use the directory in a pathname to access files and potentially subdirectories&#x20;

## Changing Basic File Permissions

The <mark style="color:red;">`chmod`</mark> command is used to change the permissions of a file or directory. **Only the **<mark style="color:red;">**`root`**</mark>** user or the user who owns the file is able to change the permissions of a file.**

```
chmod MODE FILE…
```

There are two techniques of changing permissions with the `chmod` command: _symbolic_ and _octal_. The symbolic method is good for changing one set permissions at a time. When changing more than one permission set, the octal is probably the more efficient method.

### Symbolic Method&#x20;

To use the symbolic method of the `chmod` command, use the symbols summarized in the tables below to specify the MODE argument, which represents which changes to make to the permissions.

```
chmod MODE FILE...
```

First indicate which permission group (or groups) to apply the changes to:

| Symbol | Group                                     |
| ------ | ----------------------------------------- |
| `u`    | user owner                                |
| `g`    | group owner                               |
| `o`    | others                                    |
| `a`    | all (user owner, group owner, and others) |

Next, use the following operators to indicate how to modify the permissions:

| Symbol | Operation                           |
| ------ | ----------------------------------- |
| `+`    | add the permission, if necessary    |
| `=`    | specify the exact permission        |
| `-`    | remove the permission, if necessary |

Next, use the following characters to specify the permissions type to change:

| Symbol | Permission |
| ------ | ---------- |
| `r`    | read       |
| `w`    | write      |
| `x`    | execute    |

Finally, add a space followed by the path names for the files to assign those permissions.

```bash
sysadmin@localhost:~$ chmod u+x newfile.txt
sysadmin@localhost:~$ ls -l newfile.txt
-rwxrw-r-- 1 sysadmin adm 0 Mar 21 18:10 newfile.txt

sysadmin@localhost:~$ chmod g-w newfile.txt
sysadmin@localhost:~$ ls -l newfile.txt
-rwxr--r-- 1 sysadmin adm 0 Mar 21 18:10 newfile.txt
```

To specify permissions for more than one set, use commas (and no spaces) between each set.

```
chmod MODE[,MODE]... FILE...
```

```bash
sysadmin@localhost:~$ chmod o=r,g-w,u+x newfile.txt
sysadmin@localhost:~$ ls -l newfile.txt
-rwxr--r-- 1 sysadmin nogroup 0 Mar 21 18:10 newfile.txt
```

You can also combine either the group or permissions symbols to make multiple changes to the file's permissions.

```bash
sysadmin@localhost:~$ chmod ug+x,o-r newfile.txt
```

Using the <mark style="color:red;">**`=`**</mark> character **adds the specified permissions and causes unmentioned ones to be removed.**

```
sysadmin@localhost:~$ chmod u=rx newfile.txt
sysadmin@localhost:~$ ls -l newfile.txt
-r-xr-x--- 1 sysadmin adm 0 May  3 22:26 newfile.txt
```

Assign everyone no permissions:

```
sysadmin@localhost:~$ chmod a=- fakefile
```

### Octal Method

Using the octal method requires that the permissions for all three sets be specified. It is based on the octal numbering system in which each permission type is assigned a numeric value:

| Octal Value | Permission Set |
| ----------- | -------------- |
| 7           | `rwx`          |
| 6           | `rw-`          |
| 5           | `r-x`          |
| 4           | `r--`          |
| 3           | `-wx`          |
| 2           | `-w-`          |
| 1           | `--x`          |
| 0           | `---`          |

These numbers are easy to derive by just adding together the octal value for the permissions show. So, for read, write and execute: add 4 + 2 + 1 to get 7. Or, for read, not write , and execute: add 4 + 0 + 1 to get 5.

{% hint style="warning" %}
**Consider this**

Recall that the <mark style="color:red;">**`stat`**</mark> command provides **more detailed information** than the <mark style="color:red;">`ls -l`</mark>` ``` command. One big advantage of the <mark style="color:red;">**`stat`**</mark> command is that shows permissions using both the symbolic and numeric methods:

```bash
sysadmin@localhost:~$ stat /tmp/filetest1
  File: `/tmp/filetest1'
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: fd00h/64768d	Inode: 31477       Links: 1
Access: (0664/-rw-rw-r--)  Uid: (  502/sysadmin)   Gid: (  503/sysadmin)
Access: 2013-10-21 10:18:02.809118163 -0700
Modify: 2013-10-21 10:18:02.809118163 -0700
Change: 2013-10-21 10:18:02.809118163 -0700
```
{% endhint %}

## Permission Scenarios

Keep in mind the following descriptions of how each permission behaves:

| Permission  | Effect of file                                                                                                                      | Effect of directory                                                                            |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| read `r`    | Allows processes to read the contents of the file, meaning the contents can be viewed and copied.                                   | Files names in the directoy can be listed, but other details are not available                 |
| write `w`   | Allows for contents to be written to by the process, so changes to a file can be saved. (Need the `r` permission to work correctly) | Files can be added or removed from the directory. (Needs the `r` permission to work correctly) |
| execute `x` | Allows for a file to be executed or run as a process                                                                                | Allows for the user to use the `cd` command to "get into" the directory.                       |

## Changing Advanced File Permissions

When multiple users need to work routinely on the same directories and files, basic Linux permissions may not be enough . These special permissions <mark style="color:red;">`setuid`</mark>, <mark style="color:red;">`setgid`</mark> and the <mark style="color:red;">`sticky bit`</mark> are designed to address these concerns.

Typically, special permissions are only set by the administrator (the root user) and they perform very specialized functions. They can be set using the <mark style="color:red;">`chmod`</mark> command, using either the symbolic or octal method.

| Permission                                                 | Symbol | Symbolic / Octal | Purpose                                                                                                       |
| ---------------------------------------------------------- | ------ | ---------------- | ------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">`setuid`</mark> on a file         | s      | u+s or 4000      | Causes an executable file to **execute under user owner identity**, instead of the user running the command.  |
| <mark style="color:red;">`setgid`</mark> on a file         | s      | g+s or 2000      | Causes an executable file to **execute under group owner identity**, instead of the user running the command  |
| <mark style="color:red;">`setgid`</mark> on a directory    | s      | g+s or 2000      | Causes new **files and directories that are created inside to be owned by the group** that owns the directory |
| <mark style="color:red;">`sticky bit`</mark> on a diectory | t      | o+t or 1000      | Causes files inside a directory to be **able to be removed only by the user owner, or the root user.**        |

{% hint style="info" %}
A leading 0, such as 0744, will remove all special permissions from a file or directory
{% endhint %}

## Setuid Permission

When the `setuid` permission is set on an _executable binary file_ (a program), **the binary file is run as the owner of the file**, not as the user who executed it.&#x20;

For example, the <mark style="color:red;">`passwd`</mark> command has the <mark style="color:red;">`setuid`</mark> permission. The <mark style="color:red;">`passwd`</mark> command modifies the `/etc/shadow` file in order to update the value for the user's password. That file is not normally modifiable by an ordinary user; in fact, ordinary users normally have no permissions on the file.

```
sysadmin@localhost:~$ more /etc/shadow
/etc/shadow: Permission denied
sysadmin@localhost:~$ ls -l /etc/shadow
-rw-r-----. 1 root root 5195 Oct 21 19:57 /etc/shadow
```

When the <mark style="color:red;">`passwd`</mark> command attempts to update the `/etc/shadow` file, it uses the credentials of the `root` user to modify the file (the term credentials is akin to "authority")

```bash
sysadmin@localhost:~$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 59640 Jan 25  2018 /usr/bin/passwd
```

Notice from the listing, the fourth character is an <mark style="color:red;">**`s`**</mark> ,where there would normally be an **`x`** if the file was just executable. &#x20;

{% hint style="warning" %}
When the **character is a lowercase **<mark style="color:red;">**`s`**</mark> ,it indicates that both the <mark style="color:red;">**`setuid`**</mark>** and execute permissions are set.**

An **uppercase **<mark style="color:red;">**`S`**</mark> in the fourth character position means that the file **does not have the execute permission**, only the setuid permission. (Without the execute permission for the user owner, the setuid is ineffective)&#x20;
{% endhint %}

Although not always the case ,in some instances, an uppercase <mark style="color:red;">`S`</mark> can be interpreted as an error.

To add the setuid permission run:

```bash
chmod u+s file
chomod 4755 file
```

To remove the setuid permissions run:

```bash
chmod u-s file
chmod 0755 file
```

## Setgid Permission

The <mark style="color:red;">`setgid`</mark> permission is similar to <mark style="color:red;">`setuid`</mark> ,but it makes use of the group owner permissions. There are two forms of setgid permissions:

* <mark style="color:red;">`setgid`</mark> on a file
* <mark style="color:red;">`setgid`</mark> on a directory

### `setgid` on a File

`setgid` permission will **execute as the group that owns the file**. The system allows the user running the command to effectively belong to the group that owns the file, but only in the setid program.&#x20;

```
sysadmin@localhost:~$ ls -l /usr/bin/wall
-rwxr-sr-x 1 root tty 30800 Oct 15 20:29 /usr/bin/wall
```

You can see that this file is setgid by the presence of the `s` in the _execute_ position of the _group permissions_. Due to this executable being owned by the `tty` group, when a user executes this command, the command is able to access files that are group owned by the `tty` group.

This access is important because the `/usr/bin/wall` command sends messages to terminals, which is accomplished by writing data to files like the following:

```
sysadmin@localhost:~$ ls -l /dev/tty?
crw--w----. 1 root tty  4, 0 Mar 29  2013 /dev/tty0
crw--w----. 1 root tty  4, 1 Oct 21 19:57 /dev/tty1
```

Note that the `tty` group has write permission on the files above while users who are not in the `tty` group (others) have no permissions on these files. Without the setgid permission, the `/usr/bin/wall` command would fail.

Like the setuid permission, a lowercase `s` in the execute position of the group permissions indicates that this file has both the setgid and execute permission set. An uppercase `S` instead of the `s` means the file lacks execute permission for the group. Without execute permission for the group, the setgid permission will be ineffective.

### `setgid` on Directory

When set on a directory, the `setgid` permission **cause files created in the directory to be owner by the group that owns the directory** automatically.&#x20;

In addition, if a directory is `setgid` ,then any directories created within that directory inherit the `setgid` permission.

The following example shows that the `/tmp/data` directory has the setgid permission set and that it is owned by the `demo` group.

```
sysadmin@localhost:~$ ls -ld /tmp/data
drwxrwsrwx. 2 root demo 4096 Oct 30 23:20 /tmp/data
```

Typically, files created by the user `sysadmin` are owned by their primary group, also called `sysadmin`. However, if the user `sysadmin` creates a file in the `/tmp/data` directory (the setgid directory from the preceding example), the group ownership of the file isn't the `sysadmin` group, but rather the group that owns the directory, the `demo` group:

```
sysadmin@localhost:~$  touch /tmp/data/file.txt
sysadmin@localhost:~$  ls -ld /tmp/data/file.txt
-rw-rw-r--. 1 bob demo 0 Oct 30 23:21 /tmp/data/file.txt
```

#### Setting `setgid`

```
chmod g+s <file|directory>
chmod 2775 <file|directory>
chmod g-s <file|directory>
chmod 0775 <file|directory>
```

## Sticky Bit Permission

Setting this permission can be important to prevent users from deleting other user's files.&#x20;

If an administrator wants to make a directory where any user can add a file, the directory required permissions mean that the user can also delete any other user's file in that directory.

The sticky permission on a directory **modifies the write permission on a directory so that only specific users can delete a file within the directory**:

* The users who owns the file.
* The users who owns the directory that has the sticky bit (tipically the root user)
* The root user

On a typical Linux installation, there will normally be two directories that have the sticky permission set by default: the `/tmp/` and `/var/tmp` directories.&#x20;

To set the sticky bit permission symbolically, execute a command like the following:

```
chmod o+t <directory>
chmod 1755 <file|directory>
chmod o-t <directory>
chmod 0755 <directory>
```

When a directory has the sticky permission, a `t` will replace the `x` in the set of permissions for others. For example:

```bash
sysadmin@localhost:~$ ls -ld /tmp
drwxrwxrwt 1 root root 0 Oct 17 19:17 /tmp
```

The `/tmp` directory has an octal permission mode of 1777, or a full permissions for everyone plus the sticky permission on the directory. When the other permissions set includes the execute permission, then the `t` will be in lowercase.&#x20;

There are cases in which users may not want to have the execute permission for others, but still have the sticky bit permission on a directory. For example, when creating shared directories with the `setgid` permission. Consider the following

```
sysadmin@localhost:~$ chmod 3770 /shared
```

Notice that the special permissions of `setgid` and the `sticky bit` can be added together, the 2000 permission plus the 1000 permission gives you 3000 for the special permission. The owner of the group have full access, and other get none. Listing the directory after making the changes with the `ls -ld /shared` command results in the following output:

```bash
drwxrws--T. 2 root team 4096 Jan 10 09:08 /shared
```

The uppercase `T` in the execute permission position for others indicates there is no execute permission for others. However, since multiple users of the group still have access, the sticky permission is effective for the `team` group.

## Default File Permissions

Linux sets the default permissions on these new objects based upon the value of the creator's _umask_ setting. The `umask` command is used to both set the umask value and display it.

The `umask` command is automatically executed when a shell is started. To have a persistent setting for umask, a custom `umask` command can be added to the `~/.bashrc` file.

The `umask` value only affects the permissions placed on new files and directories at the time they are created. It only affects basic permissions for the user owner, group owner, and others.&#x20;

The umask is an octal value based upon the same values that you saw earlier in this section:

| Octal Value | Permission |
| ----------- | ---------- |
| 4           | read       |
| 2           | write      |
| 1           | execute    |
| 0           | none       |

The octal value set for the `umask` is subtracted from the maximum possible permission to determine the permissions that are set when a file or directory is created.

The maximum default permissions are different for files and directories.

| Files Type  | Octal | Symbolic    |
| ----------- | ----- | ----------- |
| Files       | `666` | `rw-rw-rw-` |
| Directories | `777` | `rwxrwxrwx` |

The `umask` command can be used to display the current umask value:

```bash
sysadmin@localhost:~$ umask
0002
```

A breakdown of the output:

* The initial 0 in the umask value is for special permissions (setuid, setgid, and the sticky bit). Since those are never set by default, the initial 0 is not necessary when setting the umask value.
* The second 0 `(---)` indicates which permissions to substract from the default user owner's permissions.
* The third 0 `(---)` indicates which permissions to substract from the default group owner's permissions
* The last number 2 `(-w-)` indicates which permissions to substract from the default other's permissions.

### Understanding Umask for Files

By default, the maximum permissions that will be placed on a brand-new file are `rw-rw-rw` ,which can be represented octal as 666. The execute permission is turned off for security reasons. The `umask` value can be used to specify which of these default permissions to remove for new files.

For example, to set a umask value for new files that would remove write permissions for the owner and remove read and write permissions for the group and others, calculate the umask value as follows:

* The first digit for the user owner would be a `2` (`-w-`), which would mask only the write permission.
* The second digit would be a `6` (`rw-`), which would mask the read and write permissions.
* The third digit would be a `6` (`rw-`), which would mask the read and write permissions.

As a result, the umask value `266` would result in new files having the permissions of `r--------`.

| File Default | `666`  | `rw-rw-rw-` |
| ------------ | ------ | ----------- |
| Umask        | `-266` | `-w-rw-rw-` |
| Result       | `400`  | `r--------` |

### Understanding Umask for Directories

**The way the umask is applied to regular files is different from directory files**. For security reasons, regular files are not allowed to be executable at the time they are created.

For directories, the execute permission is critical to access the directory properly. Without the execute permission, you cannot navigate to a directory, and the write permission is not functional.  The default permissions for new directories is `rwxrwxrwx` or `777`.

For example, if you wanted a umask value for new directories that would result in full permissions for the user owner, remove or mask write permissions for the group owner, and remove all permissions from others, then you could calculate the umask value as follows:

* The first digit for the user owner would be a `0` (`---`), which would not mask any of the default permissions.
* The second digit would be a `2` (`-w-`), which would mask only the write permission.
* The third digit would be a `7` (`rwx`), which would mask all the default permissions.

As a result, the umask value `027` would result in new directories having the permissions of `rwxr-x---`.

| Directory Default | `777`  | `rwxrwxrwx` |
| ----------------- | ------ | ----------- |
| Umask             | `-027` | `----w-rwx` |
| Result            | `750`  | `rwxr-x---` |

{% hint style="warning" %}
**Very Important**

While the umask value affects the permissions for new files and directories differently (because of different maximum permissions), there is not a separate umask value for files and directories; the single umask value applies to both, as you can see from the following table of commonly used umask values:
{% endhint %}

| Umask | File Permissions | Directory Permissions | Description                |
| ----- | ---------------- | --------------------- | -------------------------- |
| `002` | `664 rw-rw-r--`  | `775 rwxrwxr-x`       | Default for ordinary users |
| `022` | `644 rw-r--r--`  | `755 rwxr-xr-x`       | Default for root user      |
| `007` | `660 rw-rw----`  | `770 rwxrwx---`       | No access for others       |
| `077` | `600 rw-------`  | `700 rwx------`       | Private to user            |

The following commands will display the current umask value, sets it to a different value and displays the new value:

```
sysadmin@localhost:~$ umask
0002
sysadmin@localhost:~$ umask 027
sysadmin@localhost:~$ umask
0027
```
