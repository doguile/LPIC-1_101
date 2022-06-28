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

The superuser do <mark style="color:red;">`sudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility **allows a user to execute a single program or command as the root or another user without knowing their password or remaining logged in as that user,** thus improving security.

> For example, if a regular user wants to install a software package, they would execute the following command:
>
> ```bash
> sysadmin@localhost:~$ sudo apt-get install bsdgames
> ```

{% hint style="info" %}
When <mark style="color:red;">`sudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> asks for a password, it **needs the current user's password**, and **not the root account password**.
{% endhint %}

The <mark style="color:orange;">`/etc/sudoers`</mark> file is **used to configure **<mark style="color:red;">**`sudo`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** and define security policies** for its use. For example, you can enable specific commands for specific users and groups from specific computers and specify if a password is required.

{% hint style="warning" %}
By default, the <mark style="color:red;">`sudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command remembers the password for 15 minutes, allowing you to execute multiple commands with `sudo` in quick succession.&#x20;

This limit can be changed by the administrator as per the security policy by changing a setting in the <mark style="color:orange;">**`/etc/sudoers`**</mark> file.
{% endhint %}

{% hint style="info" %}
The <mark style="color:orange;">`/etc/sudoers`</mark> file should be edited using the <mark style="color:red;">`visudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command as root or by using `sudo` and not a standard text editor. <mark style="color:red;">**`visudo`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** is a special editor that validates the syntax of the file before saving the changes.**
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

The <mark style="color:red;">`sudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> settings for the examples above are described below:

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

## Understanding `su`

The superuser <mark style="color:red;">`su`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to execute a shell with a different user identity**. This command is typically used by a regular user to execute a command which otherwise needs root privileges or when the root user wants to execute a command as a regular user.

> For regular user to use this command, the **password for the other account must be entered**.

Some of the key options of this command are:

| Option                                                                                                   | Meaning                                                                                                                                    |
| -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| <p><mark style="color:red;"><code>-</code></mark><br><mark style="color:red;"><code>-l</code></mark></p> | Start a new user's login shell and execute the initialization (`.rc`) files providing an environment similar to what the user would expect |
| <mark style="color:red;">`-c command`</mark>                                                             | Pass a single command to the shell. As a result, after the `su` command has completed, the user will revert back to their original shell   |
| <mark style="color:red;">`-m`</mark>                                                                     | Do not reset the values of environment variables                                                                                           |

```bash
sysadmin@localhost:~$ su -                                                      
Password:                                                                       
root@localhost:~#
```

Without specifying a username, the <mark style="color:red;">`su -`</mark> command defaults to the root user, making it equivalent to <mark style="color:red;">`su - root`</mark> , and requires the root password. The <mark style="color:red;">`su`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can be used to switch to any valid user account, provided the account password is known.

For example, to switch identities to the `user1` account and acquire the environment settings of `user1` ,execute the following command and provide the password for `user1`:

```bash
sysadmin@localhost:~$ su - joe                                                
Password:                                                                       
joe@localhost:~$ pwd                                                          
/home/joe 
joe@localhost:~$ exit                                                           
logout
```

To switch identities to `user1` account while remaining in the environment of the previous account, omit the `-`  .This tells the shell to switch UIDs but don't read the new user's initialization files.

```bash
sysadmin@localhost:~$ su joe                                                
Password:                                                                       
joe@localhost:~$      
```

{% hint style="info" %}
**Consider this**

The main differences between <mark style="color:red;">`su`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`sudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> are that <mark style="color:red;">**`su`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** switches the current user and remains that user until the account is exited**, whereas <mark style="color:red;">**`sudo`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** runs a single command with root privileges** when provided the current user's password.

Additionally, the <mark style="color:red;">`sudo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> must be set up by the administrator, while the <mark style="color:red;">`su`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command only requires knowing another user's password.
{% endhint %}

## Setting User Passwords

Usernames are used to identify users on the system. **The **<mark style="color:orange;">**`/etc/passwd`**</mark>** file stores user account information**, while the <mark style="color:orange;">**`/etc/shadow`**</mark> file **stores the encrypted password and information about aging that password** (the time before they are forced to change it). To keep system password secure, **only the root user can read and edit the **<mark style="color:orange;">**`/etc/shadow`**</mark>** file**.

User passwords are set initially by the administrator after the user account is created. The administrator uses the <mark style="color:red;">`passwd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command to change a user's password at any time. For example, if the user administrator had just created the account `testuser1` by executing <mark style="color:red;">`useradd testuser1`</mark> command, the next step to active that account would be to execute the following command:

```bash
sysadmin@localhost:~$ su -
Password:
root@localhost:~ useradd testuser1
```

Once the user is created, the administrator is able to create a password for the new user using the <mark style="color:red;">`passwd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command. After entering the following command, the administrator is asked to provide the same password twice. The password is then encrypted and recorded in the <mark style="color:orange;">`/etc/shadow`</mark> file.

```bash
root@localhost:~ passwd testuser1                                          
Enter new UNIX password:                                                        
Retype new UNIX password:                                                       
⁠⁠ passwd: password updated successfully
```

{% hint style="info" %}
The same `passwd` command is used by the root user to change a user's password should they forget
{% endhint %}

After the user account is created using the <mark style="color:red;">`useradd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command and the user's password is set with the <mark style="color:red;">`passwd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, the system administrator can enforce changing the password upon the user's first login attempt by executing the <mark style="color:red;">**`chage`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> (change password expiry) command.

```bash
root@localhost:~ chage -d 0 testuser1                                              
root@localhost:~# exit                                                          
sysadmin@localhost:~$ su testuser1                                                  
Password:                                                                       
You are required to change your password immediately (root enforced)            
Changing password for testuser1.                                                     
(current) UNIX password:
Enter new UNIX password:                                                        
Retype new UNIX password:                                                       
testuser1@localhost:/home/sysadmin$ exit
```

Changing the password can also be enforced using the `-e` option with the `passwd` command:

```bash
sysadmin@localhost:~$ sudo passwd -e testuser1
[sudo] password for sysadmin:                                      
passwd: password expiry information changed.                                    
sysadmin@localhost:~$ su testuser1                                                  
Password:                                                                       
You are required to change your password immediately (root enforced)            
(current) UNIX password:                                                        
Enter new UNIX password:                                                        
Retype new UNIX password:
testuser1@localhost:/home/sysadmin$ exit                                             
sysadmin@localhost:~$
```

The administrator can **lock a user's password**, **preventing them from accessing their account** by executing the <mark style="color:red;">**`passwd -l`**</mark> command or by executing the <mark style="color:red;">**`usermod`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command with the <mark style="color:red;">**`-L`**</mark> option. When the user logs on, the `Authentication Failure` message is displayed.

```bash
sysadmin@localhost:~$ sudo usermod -L testuser1
sysadmin@localhost:~$ su testuser1
Password:
su: Authenticatoin Failure
```

The **account can be unlocked** by assigning a password with the <mark style="color:red;">`passwd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, executing the <mark style="color:red;">**`passwd -U`**</mark> command, or by executing the <mark style="color:red;">**`usermod -U`**</mark>

```bash
sysadmin@localhost:~$ sudo usermod -U testuser1                                              
sysadmin@localhost:~$ su testuser1 
Password:
testuser1@localhost:/home/sysadmin$ exit                                                    
sysadmin@localhost:~$ 
```

{% hint style="info" %}
A regular user can change their account password using the `passwd` command, while the root user can change the password of any account using the `passwd` command.

**The **<mark style="color:red;">**`passwd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command also provides options to view password expiry.**
{% endhint %}

## Aging User Passwords

Password aging is a security feature that allows a system administrator to require users to change their passwords after specified period. When a new user account is created, the values in **the **<mark style="color:orange;">**`/etc/login.defs`**</mark>** and the **<mark style="color:orange;">**`/etc/default/useradd`**</mark>** files will determine the aging for that user's password.**

The following table lists the name of the variable, the default value and the meaning of the value for the variable in the `/etc/login.defs` file:

| Variable        | Default | Meaning                                                                                                              |
| --------------- | ------- | -------------------------------------------------------------------------------------------------------------------- |
| `PASS_MAX_DAYS` | `99999` | Maximum number of days before a user must change their password                                                      |
| `PASS_MIN_DAYS` | `0`     | Minimum number of days after the password has been changed before it can be changed again                            |
| `PASS_WARN_AGE` | `7`     | Number of days before a password's expiration that the user will begin to see warnings about setting a new password. |

The following table list the name of the variable, the default value, and the meaning of the value for the variable in the `/etc/default/useradd` file:

| Variable   | Default | Meaning                                                                                                            |
| ---------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| `INACTIVE` | `-1`    | Number of days after a password should have been changed that the user's can still log in to change their password |
| `EXPIRE`   |         | By default, there is no date set on which accounts that are created will expire                                    |

In order for the above two files to have any effect on a user account, **the values must be set in the files before the user account is created**. Options that are used with the <mark style="color:red;">`useradd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command will override any values specified in these files.&#x20;

For example, although there isn't a default **expiration date**, the administrator can override that value by specifying an <mark style="color:red;">**`-e`**</mark> or <mark style="color:red;">**`--expire`**</mark> option to the <mark style="color:red;">**`useradd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command. In adding an account for a contractor whose contract is finished on 12/31/2025, the administrator coudl have their account expire on that date by executing the following:

```bash
root@localhost:~ useradd -e 12/31/2025 contractor
root@localhost:~ chage -l contractor                                           
Last password change                                    : Nov 13, 2019          
Password expires                                        : never                 
Password inactive                                       : never                 
Account expires                                         : Dec 31, 2025          
Minimum number of days between password change          : 0                     
Maximum number of days between password change          : 99999                 
Number of days of warning before password expires       : 7
```

The following tables lists the <mark style="color:red;">**`useradd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> and <mark style="color:red;">**`usermod`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> options that can be used to affect the aging of a user's account.

| Option(s)                            | Meaning                                                                                                                                      |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">`-e`</mark> | Set the **date the account is no longer available** for use                                                                                  |
| <mark style="color:red;">`-f`</mark> | Set the **number of days the account can go beyond the password expiration** (inactive) **and the user can still log in** to change password |

Some of the password aging values of an existing account can be modified by using the <mark style="color:red;">`passwd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command when executed as the root user. The following table shows the `passwd` command options and their meaning.

| Option(s)                            | Meaning                                                                                                                                            |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">`-e`</mark> | Immediately expire the user's password                                                                                                             |
| <mark style="color:red;">`-x`</mark> | Set the **maximum number of days the user can keep a password** without updating it                                                                |
| <mark style="color:red;">`-n`</mark> | Set the **minimum number of days that a user must wait to change their password** after having updated their password                              |
| <mark style="color:red;">`-w`</mark> | Set the **number of days before the password is about to expire that the user starts to receive warnings** about changing their password           |
| <mark style="color:red;">`-i`</mark> | Set the **number of days the account can go beyond the password expiration** (inactive) and the **user can still log in** to change their password |

Although not recommended, the administrator could affect the aging user's account password by carefully editing certain fields in the <mark style="color:orange;">`/etc/shadow`</mark> file using the <mark style="color:red;">**`vipw`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> or <mark style="color:red;">**`vi`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> commands. Below are the entries for the `testuser1` and `contractor` accounts in the `shadow` file:

```bash
testuser1:$6$Q9ye8DCt$vsygxtFPurGvbkLtfSaWn.OIOByzEL7uJwxH5Prk8wyPZWgWvTQ1.u1LbQHNo8fkEyAuZI3G/b2E8jfk/IpEO/:16427:0:99999:7:::
contractor:!:16427:0:99999:7::16800:
```

The `/etc/shadow` file contains the following fields of information from teft to right separated by colons:

| Field                        | Example     | Contents                                                                                                                                       |
| ---------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| login name                   | `testuser1` | The name of the user account                                                                                                                   |
| encrypted password           | `$6$Q9...`  | The user's encrypted password. If starting with a `!` or `*`, the account is locked. An empty field indicates no password is needed to log in. |
| date of last password change | `18213`     | The date the user's password was last changed, as expressed in the number of days since January 1, 1970                                        |
| minimum password age         | `0`         | The minimum number of days the user must wait to change their password after their password has been changed                                   |
| maximum password age         | `99999`     | The maximum number of days the user can wait after which they must change their password                                                       |
| password warning period      | `7`         | The number of days before a password is about to expire that the user begins to be warned of changing their password                           |
| account expiration date      | `16800`     | The date the account will be expired as expressed in the number of days since January 1, 1970                                                  |
| reserved field               |             | Reserved for future use                                                                                                                        |

While <mark style="color:red;">`useradd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> <mark style="color:red;"></mark><mark style="color:red;">`usermod`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`passwd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command each offer some options to affect the aging of passwords, **only the **<mark style="color:red;">**`chage`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command offers the ability to change all aging settings** in addition to **displaying these values in a human-readable format.**

**The **<mark style="color:red;">**`chage`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command is the preferred way to update the aging information for an existing user account**. The command also provides an easy way to view the date values for accounts, which are stored in the `/etc/shadow` file as the number of days since January 1, 1970, in ordinary date values.&#x20;

For example , consider the following entry for the `testuser1` account:

```bash
root@localhost:~# grep testuser1 /etc/shadow                                         
testuser1:$6$It63Hhur$nVJflILTQjuDN/WwIgzsuonko/dhQDjqTLebGqAZUbY/wSUzyE3QicUR6RJPcorkuenku.Rz/4v2ZAFMmqMsv/:18213:0:99999:7:::
```

The value `18213` indicates the number of days since January 1, 1970, that the user's password was last changed. Once the administrator executes the following command, then the value will be shown as an ordinary date:

```bash
root@localhost:~ chage -l testuser1                                                
Last password change                                    : Nov 12, 2019          
Password expires                                        : never                 
Password inactive                                       : never                 
Account expires                                         : never                 
Minimum number of days between password change          : 0                     
Maximum number of days between password change          : 99999                 
Number of days of warning before password expires       : 7
```

The <mark style="color:red;">`chage`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command has the following options which allow for all of the aging information for an account to be updated:

| Option | Meaning                                                                                                                     |
| ------ | --------------------------------------------------------------------------------------------------------------------------- |
| `-d`   | Set the last date the password was changed                                                                                  |
| `-E`   | Set the account expiration date                                                                                             |
| `-I`   | Set the number of inactive days after a password expires that a user can still log in to change their password              |
| `-m`   | Set the minimum number of days after a password has been updated that the user must wait before the password can be changed |
| `-M`   | Set the maximum number of days before a password must be changed                                                            |
| `-W`   | Set the number of days before the password expires before warnings start                                                    |

The `chage` command also supports an interactive mode which can be invoked using the following command:

```bash
root@localhost:~ chage testuser1                                                   
Changing the aging information for testuser1                                        
Enter the new value, or press ENTER for the default                             
                                                                                
        Minimum Password Age [0]:                                               
        Maximum Password Age [45]:                                              
        Last Password Change (YYYY-MM-DD) [2019-11-13]:                         
        Password Expiration Warning [7]:                                        
        Password Inactive [-1]:                                                 
        Account Expiration Date (YYYY-MM-DD) [-1]:
```

## Assessing Network Security

The <mark style="color:red;">`nmap`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is an open source **tool used by system administrator for auditing networks**, **security scannings**, and **finding open ports on host machines**. It is capable of scanning a host or the entire subnet to find open **TCP** and **UDP** ports. This tool is also used by attackers to find vulnerables ports.

```
root@localhost:~# nmap example.com     

Starting Nmap 6.40 ( http://nmap.org ) at 2015-03-13 22:02 UTC                  
Nmap scan report for example.com (192.168.1.3)                                  
Host is up (0.000013s latency).                                                 
Not shown: 998 closed ports                                                     
PORT   STATE SERVICE                                                            
22/tcp open  ssh                                                                
53/tcp open  domain                                                             
                                                                                
Nmap done: 1 IP address (1 host up) scanned in 2.49 seconds
```

This output mentions the **`PORT` ** number, the protocol used, the **`STATE` ** of the port, and the **`SERVICE` ** using the port. The state of the port can be:

| State        | Meaning                                                                                                                                                 |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `open`       | Application on the target host is listening for incoming packets on this port                                                                           |
| `closed`     | No applications are listening on this port                                                                                                              |
| `filtered`   | The `nmap` command cannot identify if the port is open or closed because a network-level firewall or similar filter is not allowing probes to this port |
| `unfiltered` | The `nmap` command can probe this port but does not have adequate information to conclude if it is open or closed                                       |

The network status <mark style="color:red;">**`netstat`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command provides a wealth of information about **network connections, interface statistics, routing tables**, and many other details of the network configuration. It is designed to be run on the local system; <mark style="background-color:red;">it does not scan ports on remote systems.</mark>

In assessing network security, the most useful options of the <mark style="color:red;">`netstat`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command are those which show the network ports that are currently open and the state of the connections.

The following table shows the options of the `netstat` command, which are useful for viewing network ports:

| Option | Meaning                                |
| ------ | -------------------------------------- |
| `-l`   | Display sockets that are listening     |
| `-a`   | Display all sockets                    |
| `-n`   | Don't resolve host, port or user names |
| `-e`   | Display extended information process   |
| `-p`   | Display PID and program name           |
| `-t`   | Display TCP sockets                    |
| `-u`   | Display UDP sockets                    |

To view all open ports and the processes which opened them, execute the following command:

```
netstat -tulnp
Active Internet connections (only servers)                                      
Proto Recv-Q Send-Q Local Address           Foreign Address         State       
PID/Program name                                                                
tcp        0      0 127.0.0.11:33314        0.0.0.0:*               LISTEN      
-                                                                               
tcp        0      0 192.168.1.2:53          0.0.0.0:*               LISTEN      
-                                                                               
tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN      
-                                                                               
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      
16/sshd                                                                         
tcp        0      0 127.0.0.1:953           0.0.0.0:*               LISTEN      
-                                                                               
tcp6       0      0 :::53                   :::*                    LISTEN      
-                                                                               
tcp6       0      0 :::22                   :::*                    LISTEN    
```

The protocol, the port number, the process name ,and PID are the key fields in the output above.

## Processes Accessing Files

To secure a system, it is esential to know which processes are running on the system and their interaction with other hosts on the network. The list open files `lsof` command is a useful utility for viewing files opened by active processes.

> The files included are: regular files, directories ,block special files, character special files, libraries, and network sockets

To view all open files on the system, execute the following command:

```
root@localhost:~# lsof | more
COMMAND   PID TID     USER   FD      TYPE    DEVICE SIZE/OFF      NODE NAME     
init        1         root  cwd       DIR     0,190     4096 100037959 /        
init        1         root  rtd       DIR     0,190     4096 100037959 /        
init        1         root  txt       REG       8,2     8440  73798352 /sbin/init                                                                               
init        1         root  mem       REG       8,2  2030544  73139866 /lib/x86_
64-linux-gnu/libc-2.27.so                                                       
init        1         root  mem       REG       8,2   170960  73139853 /lib/x86_
64-linux-gnu/ld-2.27.so                                                         
init        1         root    0u      CHR    136,26      0t0        29 /26      
init        1         root    1u      CHR    136,26      0t0        29 /26      
init        1         root    2u      CHR    136,26      0t0        29 /26      
login       7         root  cwd   unknown                              /proc/7/c
```

To **list all files opened by a particular user**, execute the following command:

```bash
lsof -u sysadmin
```

To **list all programs listening ports**, execute the following command:

```bash
lsof -i
```

To **list all TCP connectins**, execute the following

```bash
lsof -i TCP
```

If the applications wants to use a particular port and the error message that is shown is _port already in use_, **check which process is currently using this port** by executing the following command:

```bash
lsof -i TCP:25
```

To **check all connections to and from a particular host**, execute the following command:

```bash
lsof -i @127.0.0.1
```

#### fuser

The file user <mark style="color:red;">`fuser`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can also be used to **display information about open files and sockets being accessed by processes**.&#x20;

The <mark style="color:red;">`lsof`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **appropiate if a user needs to list all or multiple files being accessed**, whereas the <mark style="color:red;">`fuser`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command can be **used to list processes accessing a specific file**. The <mark style="color:red;">`fuser`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command also has the **ability to kill processes accessing a file** using the <mark style="color:red;">`-k`</mark> option.

To use the <mark style="color:red;">`fuser`</mark> command, **specify a file or directory as an argument.** Used without options, the <mark style="color:red;">`fuser`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command will display only the PIDs of processes accessing the file:

```bash
root@localhost:~ fuser /
Output omitted...
/:                       1rc    13r    16rc    82r
```

In the output above, the directory `/` is being accessed by processes with the PIDs `1`, `13` , `16` and `82`

To display additional information such as process names, user the verbose `-v` option. The command below will display all (`-a`) processes accessing the `/` directory

```
root@localhost:~ fuser -av /
Output Omitted...
                   USER      PID ACCESS COMMAND          
/:                 root   kernel mount /
                   root        1 .rc.. init
                   root       13 .r... cron
                   root       16 .rc.. sshd
                   root       82 .r... bash
```

The access types under the `ACCESS` column include `r` for root directory and `c` for current directory.

Access codes that might be reported by the `fuser` commands are:

| Access Code | Meaning                                                                            |
| ----------- | ---------------------------------------------------------------------------------- |
| `c`         | The process is using the mount point or a subdirectory as its current directory.   |
| `e`         | The process is an executable file that resides in the mount point structure.       |
| `f`         | The process has an open file from the mount point structure.                       |
| `F`         | The process has an open file from the mount point structure that it is writing to. |
| `r`         | The process is using the mount point as the root directory.                        |
| `m`         | The process is a memory-mapped (mmap) file or shared library.                      |

## Settings User Limits

The user limit `ulimit` command is used to control resources that can be assigned by a user's login shell and child processes spawned from the shell. The system administrator may need to regulate the use of shared resources to prevent one process from using too much of a resource, preventing another process or user from having sufficient access to that resoruce.

The `ulimit` command is used to set limits at the user level and these limits are applicable to all processes running for that user.

There can be two types of limits; hard limits and soft limits.&#x20;

* Hard limits are set by the root user
* Soft limits can be set by either the root user or a regular user can set their own soft limit. The main constraint is htat soft limits cannot exceed hard limits.

To **view the currently set limits for a user's accounts**, you must be logged in as the user and execute the <mark style="color:red;">**`ulimit -a`**</mark> command:

```
sysadmin@localhost:~$ ulimit -a                                                 
core file size          (blocks, -c) 0                                          
data seg size           (kbytes, -d) unlimited                                  
scheduling priority             (-e) 0                                          
file size               (blocks, -f) unlimited                                  
pending signals                 (-i) 515165                                     
max locked memory       (kbytes, -l) 64                                         
max memory size         (kbytes, -m) unlimited                                  
open files                      (-n) 1024                                       
pipe size            (512 bytes, -p) 8                                          
POSIX message queues     (bytes, -q) 819200                                     
real-time priority              (-r) 0                                          
stack size              (kbytes, -s) 8192                                       
cpu time               (seconds, -t) unlimited                                  
max user processes              (-u) 1048576                                  
virtual memory          (kbytes, -v) unlimited                                  
file locks                      (-x) unlimited
```

Creation of core files of unlimited size for testing purpposes can be enabled, using the `-c` option:

```bash
sysadmin@localhost:~$ ulimit -c unlimited                                       
sysadmin@localhost:~$ ulimit -a                                                 
core file size          (blocks, -c) unlimited
Output omitted...
```

The `ulimit` command options are provided in parenthesis as part of the `ulimit -a` output.

> For example, (`-u`) indicates the `-u` option is used to set the maximum number of concurrent processes the user can have.
>
> Useful in preventing a hacker from creating a fork bomb, which is a form of DoS attack

Based on the `ulimit -a` output above, the user can currently execute 1048576 processes:

```
max user processes              (-u) 1048576
```

To reduce this limit, execute `ulimit -u 512` command.

```
sysadmin@localhost:~$ ulimit -u 512                                             
sysadmin@localhost:~$ ulimit -a                                                 
core file size          (blocks, -c) unlimited                                  
Output omitted... 
max user processes              (-u) 512
```

Some of the key options of `ulimit` are as follows:

| Option | Meaning                                                |
| ------ | ------------------------------------------------------ |
| `-c`   | Maximum size of core files created                     |
| `-d`   | Maximum size of the process’s data segment             |
| `-s`   | Maximum stack size                                     |
| `-u`   | Maximum number of processes available to a single user |
| `-v`   | Maximum virtual memory available to the user’s process |
| `-l`   | Maximum size that may be locked into memory            |

While it is possible for the administrator to interactively set limits with the `ulimit` command, the `/etc/security/limits.conf` file can be used to set permanent limits for users that are enforced when the user logs in. Also, files that have a file name that matches the glob `*.conf` may be placed in the `/etc/security/limits.d` directory; these files can contain entries to make limits persistent.

The syntax of entries for these configuration files follows the pattern:

```
<domain> <type> <item> <value>
```

The `domain` represents the users to which the limits apply. The following can be used as a domain:

* A username
* A group name prefixed with the `@` symbol
* An asterisk `*` to apply to every user
* A UID range as min_uid:max_uid, i.e: `500:1000`
* A GID range as `@min_gid:max_gid` i.e `@1000-5000`

Then `type` is the kind of limit that is being set. The limit type can be:

* `-`
* `hard`
* `soft`

Hard limits are enforced by the kernel, and a user cannot use resources beyond such a limit. Soft limits can be thought of as defaults limits for normal usage, and the user can increase or decrease the limit within the range permitted by the hard limit. The `-` is used to enforce setting the soft and hard limits together.

The `item` is the resource that is being restricted. The `value` displayed is the hard (actual) limit. The following is a list of the items that can be restricted with limits:

* `core:` limits the core file size (KB)
* `data:` max data size (KB)
* `fsize:` maximum filesize (KB)
* `memlock:` max locked-in-memory address space (KB)
* `nofile:` max number of open files
* `rss:` max resident set size (KB)
* `stack:` max stack size (KB)
* `cpu:` max CPU time (MIN)
* `nproc:` max number of processes
* `as:` address space limit (KB)
* `maxlogins:` max number of logins for this user
* `maxsyslogins:` max number of logins on the system
* `priority:` the priority to run user process with
* `locks:` max number of file locks the user can hold
* `sigpending:` max number of pending signals
* `msgqueue:` max memory used by POSIX message queues (bytes)
* `nice:` max nice priority allowed to raise to, values: \[`-20, 19`]
* `rtprio:` max realtime priority

If a new limit is set in the configuratin files, the limit will not apply until the next time the user logs into the system.

The `/etc/security/limits.conf` provides the syntax with examples entries denoted by lines that start with the comment # symbol.

```
sysadmin@localhost:~$ tail -n 14 /etc/security/limits.conf                      
#<domain>      <type>  <item>         <value>                                   
#                                                                               

#*               soft    core            0                                      
#root            hard    core            100000                                 
#*               hard    rss             10000                                  
#@student        hard    nproc           20                                     
#@faculty        soft    nproc           20                                     
#@faculty        hard    nproc           50                                     
#ftp             hard    nproc           0                                      
#ftp             -       chroot          /ftp                                   
#@student        -       maxlogins       4                                      

# End of file
```

















