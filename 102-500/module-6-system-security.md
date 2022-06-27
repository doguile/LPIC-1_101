# Module 6: System Security

## Chapter 16: Account Security

> **110.1: Perform security administration tasks v2**
>
> Weight: 3
>
> Candidates should know how to review system configuration to ensure host security in accordance with local security policies.

<details>

<summary>Key terms</summary>

`/etc/sudoers` System file used to **configure sudo and define security policies** for its use. For example, you can enable specific commands for specific users and groups from specific computers and specify if a password is required.

`find` Command used recursively search for files that match a Boolean expression. Especially **useful for auditing files which have the SUID** (Set User ID) permission bit set which makes a system vulnerable.

`fuser` The file user fuser command can also be **used to display information about open files and sockets being accessed by processes**.

`lsof` The list open files lsof commands is a **useful utility for viewing files opened by active processes.**

`su` The su (substitute user) command is **used to execute a shell with a different user and group identities.** This command is typically used by a regular user to execute a command which otherwise needs root privileges or when the root user wants to execute a command as a regular user. For regular user to use this command, the password for the other account must be entered.

`sudo` The sudo (substitute user do) utility **allows a user to execute a single program or commands as the root** or another user without knowing their password or remaining logged in as that user, thus improving security. This utility is used commonly to execute programs which require root privileges.

`ulimit` The ulimit (user limit) command is **used to control resources that can be assigned by user's login shell and child processes spawned from the shell**. The ulimit command is used to set "hard" and "soft" limits at the user level and these limits are applicable to all processes running for that user.

`who, w, last` The `who` command displays a list of users who are currently logged into the system, where they are logged in from, and when they logged in. The `w` command provides a more detailed list about the users currently on the system than the who command. The `last` command reads the entire login history from the `/var/log/wtmp` file and displays all logins and reboot records by default.

</details>

## Understanding SUID/SGID

The file system stores the owner UID (User ID) and GID (Group ID) of the file in the inode, a location in the file system that is used to stroe data associated with files.

There are different types of UIDs supported by Linux to facilitate user management:

* **Real User ID**: The ID assigned by the system when a user logs in. All processes which are started by the user account will inherit the user's real user ID. **The real user ID can be displayed with the **<mark style="color:red;">**`id -u -r`**</mark>** command.**
* **Effective User ID:** The ID used by the system to determine the level of access the current process has. **The **<mark style="color:red;">**`setuid`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** permission and **<mark style="color:red;">**`su`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command, change the effective user ID of a program**, providing a means for a user to run a program or access a file as another user without having to log off and log in to another user account. **The effective user ID is displayed with the **<mark style="color:red;">**`id`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command.**
* **Saved User ID:** A placeholder used for switching back and forth between real and effective UIDs. It is used when a program running with elevated privileges needs to do some unprivileged work temporarily; it changes its effective user ID from a privileged value to some unprivileged one.

Normally, when a user accesses or executes a file, the user's real UID and GID are used to determine the level of access when executing the procedure.

> When an executable file has the SUID (setuid) permission, then the owner of the executable file becomes the Effective User ID to determine access and execute the procedure.
>
> Likewise, when a file has the SGID (setgid) permission, the group owner identity of the file is used as the Effective Group ID to determine file access and execute the procedure.

If a file has the setuid on an executable file, the output of the `ls -l` command for the file will display an `s` in th executable column.

```bash
sysadmin@localhost:~$ ls -l /usr/bin/passwd                                     
-rwsr-xr-x 1 root root 59640 Jan 25  2018 /usr/bin/passwd   
```

> If the setiud was not set, a regular user would not be able to use the `passwd` command to write the new password to the `/etc/shadow` file since the file owner is root.

The setuid allows a regular user to temporarily become root so the new password cna be written to the `shadow` file.

To add the setuid permission to a file, the administrator can execute the following command:

```
chmod u+s file
```

```bash
sysadmin@localhost:~$ chmod u+s ./junk.txt
sysadmin@localhost:~$ ls -l ./junk.txt
-rwSr--r-- 1 root root 28 Dec 16 2018 ./junk.txt
```

{% hint style="info" %}
An **uppercase **<mark style="color:red;">**`S`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> means that the file **does not have the execute permission set**, only the setuid permission. When the character is a **lowercase **<mark style="color:red;">**`s`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ,it indicates that both the **setuid and execute permissions are set**.
{% endhint %}

To remove the setuid permission from a file, the administrator can execute the following command:

```
chmod u-s file
```

As an alternative, the **setuid bit can be set by adding the special permission octal mode of **<mark style="color:red;">**`4000`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> in addition to the ordinary permissions for owner group, and other. For example, to set a file with the permissions of `rwsr-xr-x` , use the octal mode of `4755`&#x20;

Other commands, like `wall` command, have the **setgid bit set**, so the command runs with the group owner of the command instead of the group that the user executing the command belongs to. Files that have setgid bit set will have an <mark style="color:red;">**`s`**</mark> in the execute column for the group permissions, so the permissions on a setgid file will look similar to:

```bash
root@localhost:~# ls -l /usr/bin/wall                                       
-rwxr-sr-x 1 root tty 30800 Oct 15  2018 /usr/bin/wall    
```

To add the setgid permission to a file, the administrator can execute:

```
chmod g+s file
```

```bash
sysadmin@localhost:~$ chmod g+s ./junk.txt
sysadmin@localhost:~$ ls -l ./junk.txt
-rwxr-Sr-- 1 root root 28 Dec 16 2018 ./junk.txt
```

To remove the setgid permission from a file, the administrator can execute:

```
chmod g-s file
```

As an alternative, the **setgid** can be set by adding the **special permission octal mode** of <mark style="color:red;">`2000`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> in addition to the ordinary permissions for owner group, and other. For example, to set a file with the permissions of `rwxr-sr-x` ,use the octal mode of <mark style="color:red;">**`2755`**</mark>

**When a parent process spawns a child process, the child process inherits the security context of the parent.** So, if a program is setuid, any child processes spawned by it will also be setuid and likely have access to all resources accessible to the root user.

{% hint style="info" %}
When the user starts a program that does not have the SUID bit set, then the real, effective, and saved user ID will be initialized to the user's UID. But if the SUID bit is set, then the effective user ID is set to the UID of the owner of the file that is being executed, while the real UID is unchanged.
{% endhint %}

## Auditing SUID/SGID Files

Since SUID/SGID files make a system vulnerable, the system administrator needs to keep track of these files. To find files which have the **setuid** bit set, execute the following command:

```bash
find / -type f -perm -4000 2>/dev/null
```

> In the example, the `find` command is used with the <mark style="color:red;">`-type`</mark> and <mark style="color:red;">`-perm`</mark> options to look for files with the suid permission set in root (`/`) folder.

To find files which have the **setgid** bit set, execute the following command.

```bash
find / -type f -perm -2000 2>/dev/null
```

If the administrator wnats to locate either SUID or SGID files, then a logical OR condition (<mark style="color:red;">`-o`</mark>) can be added to the <mark style="color:red;">`find`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command. If the administrator wants to differentiate the SUID or SGID type files, then the <mark style="color:red;">`-ls`</mark> output option can be added to show a detailed listing of the files.

```bash
find / -type f -perm 4000 -o -perm 2000 -ls 2>/dev/null
200540742     12 -rwxr-sr-x   1 root     utmp        10232 Mar 11  2016 /usr/lib
/x86_64-linux-gnu/utempter/utempter                                             
200415853     12 -rwxr-sr-x   1 root     tty         10232 Aug  4  2017 /usr/lib
/mc/cons.saver                                                                  
200016721     32 -rwxr-sr-x   1 root     tty         30800 Oct 15  2018 /usr/bin
/wall                                                                           
200016319     72 -rwxr-sr-x   1 root     shadow      71816 Jan 25  2018 /usr/bin
/chage                                                                          
200016386     24 -rwxr-sr-x   1 root     shadow      22808 Jan 25  2018 /usr/bin
/expiry                                                                         
200016310     16 -rwxr-sr-x   1 root     tty         14328 Jan 17  2018 /usr/bin
```

Redirect the output of this `find` command to a file to create a baseline to audit changes in the SUID or SGID files.

In the future, you could run the `find` command again and compare the results to the file that you created to see if any new files have either the setuid or setgid bit set.

```bash
find / -perm 4000 -o -perm 2000 -ls 2>/dev/null > ~/current.perm; diff ~/special.perm ~/current.perm
```

## Configuring `sudo`

The superuser do `sudo` utility allows a user to execute a single program or command as the root or another user without knowing their password or remaining logged in as that user, thus improving security.

> For example, if a regular user wants to install a software package, they would execute the following command:
>
> ```
> sysadmin@localhost:~$ sudo apt-get install bsdgames
> ```

{% hint style="info" %}
When `sudo` asks for a password, it needs the current user's password, and not the root account password.
{% endhint %}

The `/etc/sudoers` file is used to configure `sudo` and define security policies for its use. For example, you can enable specific commands for specific users and groups from specific computers and specify if a password is required.

{% hint style="warning" %}
By default, the `sudo` command remembers the password for 15 minutes, allowing you to execute multiple commands with `sudo` in quick succession.&#x20;

This limit can be changed by the administrator as per the security policy by changing a setting in the <mark style="color:orange;">**`/etc/sudoers`**</mark> file.
{% endhint %}

{% hint style="info" %}
The `/etc/sudoers` file should be edited using the <mark style="color:red;">`visudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command as root or by using `sudo` and not a standard text editor. <mark style="color:red;">**`visudo`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** is a special editor that validates the syntax of the file before saving the changes.**
{% endhint %}

In addition to customized settings, `/etc/sudoers` contains two types of entries:

* Aliases: The configuration allows four types of aliases:
  * `User_Alias`
  * `Host_Alias`
  * `Runas_Alias`
  * `Cmnd_Alias`

The entries in the file will look like the following:

```bash
User_Alias     OPERATORS = user1, user2, user3
	Host_Alias     DBNET = 172.16.0.0/255.255.224.0
	Runas_Alias    OP = root, operator
	Cmnd_Alias     EDITORS = /usr/bin/vim, /usr/bin/nano
```

| Alias Type | Definition                                                                                                                                                                             |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `User`     | <p>Specifies groups of users.</p><p>Usernames, system groups (prefixed by a percent <code>%</code> sign), and netgroups (prefixed by a plus <code>+</code> sign) can be specified.</p> |
| `Host`     | Specifies a list of hostname, IP addresses, networks, and netgroups (prefixed with a plus `+` sign).                                                                                   |
| `Runas`    | Similar to user aliases but accepts UIDs instead of username. Better for matching multiple user names and groups having different names but the same UID.                              |
| `Command`  | <p>Specifies a list of commands and directories.</p><p>Specifying a directory will include all files within that directory but no subdirectories.</p>                                  |

* Specifications define which users cna execute which programs, these look like the following:

```bash
OPERATORS ALL=ALL
testuser1 DBNET=(ALL) ALL
testuser2 ALL= EDITORS
```

The `sudo` settings for the examples above are described below:

* The 1st line indicates that users who are part of the `OPERATORS` groups can execute any command. The first `ALL` indicates which machines the rule applies to, and the second `ALL` indicates which commands can be executed.
* The 2nd line indicates that `testuser1` can run any command as any user on any host taht is in the `DBNET` network
* The 3rd line indicates that `testuser2` can run the `vim` and `nano` editors as either the root user or any other user on the system

{% hint style="info" %}
Consider this

There are several key advantages of using `sudo` instead of logging in as root:

* The root password is not exposed to users.
* THe amount of time that users spend with root privileges is strictly restricted to the command execution only
* User that are executing the commands as root are logged.
{% endhint %}

Some of the key options of the `sudo`  command are:

| Option                                         | Meaning                                                               |
| ---------------------------------------------- | --------------------------------------------------------------------- |
| <mark style="color:red;">`-b`</mark>           | Execute the command in background                                     |
| <mark style="color:red;">`-u user_name`</mark> | Execute the command as the specified user instead of as the root user |
| <mark style="color:red;">`-k`</mark>           | Invalidates the user’s cached credentials                             |
| <mark style="color:red;">`-v`</mark>           | Update the user’s cached credentials                                  |
| <mark style="color:red;">`-n`</mark>           | Do not prompt the user for their password                             |



