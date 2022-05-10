# Module 3: User and System Administration

## Chapter 6: User and Group Accounts

> **107.1: Manage user and group accounts and related system files v2**
>
> Weight: 5

<details>

<summary>Key terms</summary>

**`/etc/group`** System file that defines the groups a user belongs plus various system groups automatically created as part of the Linux installation

**`/etc/passwd`** System file containing details of special system accounts used to run services, and user accounts on the system.

**`/etc/shadow`** System file containing the encrypted password and password aging information. Viewable only by the root user

**`/etc/skel`** System directory containing a default set of "skeleton" (template) files and directories which are copied by default to the home directory of a user that is created with the `useradd` command. This provide a uniform directory structure for all new users of the system

**`chage`** Command used by root to update information related to password expiry. Using this command, the administrator can enforce a password changing and expiry policy for specific user accounts

**`getent`** An alternative to the groups command that displays a users group record in /etc/group. Also used to queary other administrative files (passwd, shadow, hosts, etc) called databases which store user information

**`groupadd`** Command used to add a new group. By default the groupadd command will automatically assigns a GID to the new group automatically by using the next available GID

**`groupdel`** Command used to delete a group that is no longer needed. You cannot delete a group if it is a user's primary group before first deleting the user with the `userdel` command

**`groupmod`** Command used to modify the properties (i.e GID) of an existing group account.

**`passwd`** Command is used to update a user's password.

**`useradd`** Command used by root to add a new user account to the system. The `useradd -D` command displays the default options (home directory, login shell, etc) if they are not specified when useradd is used.

**`userdel`** Command used by root to delete user account information preventing them from logging in and optionally removing all and the files related the account.

**`usermod`** Commnad used by root to modify existing a user account parameters such as home directory, loginname, login shell, password aging information, etc.

</details>

## Introduction

Daily tasks can include creating and maintaining accounts, installing software, ensuring system security, backing up data, and solving computer and network problems. This chapter focuses on creating and maintaining user and group accounts, password security, automating administration and maintenance tasks, and localization features for system anywhere in the world.

Linux is a multi-user operating system, allowing multiple users to access the system simultaneously. User and group accounts facilitate this by controlling the access allowed for different type of users.

## User and System Account Files

#### The `/etc/passwd` File

The **`/etc/passwd`** file contains the details of special system accounts used to run services, and user accounts on the system. Without an entry in the **`/etc/passwd`** file, users are unable to log in to the system. **This file has read access for all users, but write access is limited to the root user.**

```bash
sysadmin@localhost:~$ cat /etc/passwd                                         
root:x:0:0:root:/root:/bin/bash                                                 
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin                                 
bin:x:2:2:bin:/bin:/usr/sbin/nologin                                            
sys:x:3:3:sys:/dev:/usr/sbin/nologin                                            
sync:x:4:65534:sync:/bin:/bin/sync                                              
games:x:5:60:games:/usr/games:/usr/sbin/nologin                                 
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin                                 
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin                                    
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin                                     
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin                               
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin                             
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin                                      
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

Each row in this file describes one user account record. Each record in the `passwd` file contains the following seven fields separated by colons:

```bash
loginID:x:UID:GID:comment:home_directory:login_shell
```

| Field          | Example                    | Significance                                                                                                |
| -------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Login ID       | `sysadmin`                 | Login name of the user                                                                                      |
| Password       | `x`                        | `x` indicates that the encrypted password has been stored in `/etc/shadow` file                             |
| UID            | `1001`                     | User ID assigned by the system (this is a non-zero value for regular users since **root always has UID 0**) |
| GID            | `1001`                     | Primary Group ID                                                                                            |
| GECOS          | `System Administrator,,,,` | Comments, if any, are specified in this field. Note that commas are not delimiters, only colons             |
| Home Directory | `/home/sysadmin`           | Absolute path of the default home directory of the user                                                     |
| Shell          | `/bin/bash`                | Absolute path of the user’s login shell                                                                     |

{% hint style="info" %}
The **GECOS** (General Electric Comprehensive Operating System) field **can contain general information about the user**, such as contact number and full name. Colons are not allowed in this field.

Most administrators use this field for simple comments, but there is a more formal format that can be used.

Regulars users can modify this information for their own account with the <mark style="color:red;">**`chfn`**</mark> command:

```bash
sysadmin@localhost:~$ chfn                                                      
Password:                                                                       
Changing the user information for sysadmin                                      
Enter the new value, or press ENTER for the default                             
        Full Name: System Administrator                                         
        Room Number []: A-110                                                   
        Work Phone []: 555-1212                                                 
        Home Phone []:                                                          
sysadmin@localhost:~$ grep sysadmin /etc/passwd                                 
sysadmin:x:1001:1001:System Administrator,A-110,555-1212,:/home/sysadmin:/bin/bash
```



The <mark style="color:red;">**`finger`**</mark> command can be used by other users to display this information

```bash
sysadmin@localhost:~$ finger sysadmin
Login: sysadmin                         Name: Dan Smith
Directory: /home/sysadmin               Shell: /bin/bash
Office: A-110, 555-555-5555             Home Phone: 555-555-5555
On since Sat Mar  8 09:38 (PST) on ttyl from :0
    1 day 9 hours idle
On since Sun Mar  9 11:39 (PDT) on pets/l from :0.0
No mail.
No Plan.
```
{% endhint %}

#### The `/etc/shadow` File

The `/etc/shadow` file contains the encrypted password of the user and some parameters related to password security. In addition to providing the administrator with the ability to set password expiration fields.

{% hint style="warning" %}
Only the root user can view the `/etc/shadow` file
{% endhint %}

For every user entry in the `/etc/passwd` file, an entry exists in the `/etc/shadow` file. The `/etc/passwd` file only contains an `*` or `x` in the password field as a placeholder for the user's encrypted password stored in the `/etc/shadow` file.

```bash
root@localhost:~ tail /etc/shadow                                     
uuidd:*:17977:0:99999:7:::                                                      
syslog:*:17977:0:99999:7:::                                                     
messagebus:*:17977:0:99999:7:::                                                 
bind:*:18003:0:99999:7:::                                                       
sshd:*:18003:0:99999:7:::                                                       
operator:!:18010:0:99999:7:::                                                   
sysadmin:$6$8.BhF.gs$gVnGSxU/JM4mcKgdbvgIOPWGT5XN25cZwEyI4W3/Nvh0p14UrUrSuvqp3SpPBBq9xYGpmbCAYPHfLgYLpnmL81:18010:0:99999:7:::                                  
alice:!:18018:0:99999:7:::                                                      
maya:!:18018:0:99999:7:::                                                       
joe:!:18018:0:99999:7::: 
```

Each record in the `/etc/shadow` file contains the following eight fields, separated by colons:

```bash
loginID:password:lastchg:min:max:warn:inactive:expire
```

| Field                | Example                                                                                                | Significance                                                                                                                                                                                                                                                                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Username             | `sysadmin`                                                                                             | Login name of the user                                                                                                                                                                                                                                                                                                                                      |
| Password             | `$6$trs.W5GR$sgPBwHSIR6Zfcw7oe05mr mtR3slZQRDur02NZ71OmZ7Uaz0Xrl0ME 7tdMC1Y9rrX5Jlh1zASnXFCuYFgNHzU/0` | <p>Encrypted password</p><p>Note: An empty field indicates no password is required to log in. <strong>An asterisk <code>*</code> or exclamation mark <code>!</code> indicates an inaccessible account  with no password</strong>. A password field that starts with <code>!</code> followed by the encrypted password, indicates the password is locked</p> |
| Last password change | `16413`                                                                                                | Number of days between January 1, 1970, and the last password change                                                                                                                                                                                                                                                                                        |
| Minimum              | `0`                                                                                                    | Minimum number of days that the current password can be changed by the user; a value of `0` means “no minimum password age”                                                                                                                                                                                                                                 |
| Maximum              | `99999`                                                                                                | Maximum number of days remaining for the password to expire; a value of `99999` means “no maximum password age”                                                                                                                                                                                                                                             |
| Warn                 | `7`                                                                                                    | Number of days prior to password expiry that the user is warned                                                                                                                                                                                                                                                                                             |
| Inactive             | `30`                                                                                                   | Number of days after password expiry that the user account remains active                                                                                                                                                                                                                                                                                   |
| Expire               | `17000`                                                                                                | Date indicating when the password is deleted and user name will become inactive                                                                                                                                                                                                                                                                             |
| Reserved field       |                                                                                                        | Reserved for future use                                                                                                                                                                                                                                                                                                                                     |

## Special Purpose System Accounts

As introduced earlier, Linux system come with special purpose system accounts for `root` ,`ssh`,`ftp`, `mail`, and other system users. These accounts are defined in`/etc/passwd` and are not accessible to regular users.

```bash
sysadmin@localhost:~$ cat /etc/passwd                                         
root:x:0:0:root:/root:/bin/bash                                                 
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin                                 
bin:x:2:2:bin:/bin:/usr/sbin/nologin                                            
sys:x:3:3:sys:/dev:/usr/sbin/nologin                                            
sync:x:4:65534:sync:/bin:/bin/sync                                              
games:x:5:60:games:/usr/games:/usr/sbin/nologin                                 
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin                                 
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin                                    
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin                                     
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin                               
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin                             
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin                                      
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin  
```

**These accounts are used for running services**, which are closely linked with a specific set of programs or applications. **System accounts will be managed by the root user or admin group users** only.

{% hint style="info" %}
In traditional **UNIX** operating systems, accounts with UID numbers **under 100** were reserved for system accounts. In **Red Hat-based** Linux distributions, the reserverd range of system accounts is from 0-**499**. On **Debian-based Linux** distributions, that range has been increased to **999** to accommodate the need for additional system accounts.
{% endhint %}

The **`/etc/login.defs`** file contains a setting that allows the administrator to specify which number the regular user accounts start from:

```bash
sysadmin@localhost:~$ cat /etc/login.defs | grep UID                            
UID_MIN               1000                                                   
UID_MAX               60000                                                   
#SYS_UID_MIN          100                                                   
#SYS_UID_MAX          999
```

The `UID_MIN` setting means that when the first new user account is created, the lowest possible `UID` number that will be automatically assigned to the account will be `1000` (Red Hat-based system start at 500). **The **<mark style="color:red;">**`UID`**</mark>** number is automatically increased by one each time additional new user accounts are created.**

This concept also applies to group accounts; the `GID_MIN` setting in the `/etc/login.defs` file indicates the lowest possible GID number assigned to the first regular group account is `1000`:

```bash
sysadmin@localhost:~$ cat /etc/login.defs | grep GID                            
GID_MIN                1000                                                   
GID_MAX                60000                                                   
#SYS_GID_MIN           100                                                   
#SYS_GID_MAX           999
```

## User Accounts

Creating a new user is a two-step process:

1. Create a new account
2. Set a password for the new account

The <mark style="color:red;">**`useradd`**</mark> command is used to **create a new user account**. To add a new user named `test_user1`, run the following command as `root`:

```bash
sysadmin@localhost:~$ su -
Password:
root@localhost:~# useradd test_user1
```

{% hint style="info" %}
Note that when executed successfully, the <mark style="color:red;">**`useradd`**</mark> command **produces no output**. To verify the command was successful, use the `tail -1` command to display the `/etc/passwd` and `/etc/shadow` files

```bash
root@localhost:~ tail -1 /etc/passwd                                           
test_user1:x:1005:1008::/home/test_user1:/bin/bash 
root@localhost:~ tail -1 /etc/shadow                                           
test_user1:!:18162:0:99999:7:::
```
{% endhint %}

Notice the password field in the `/etc/shadow` for the new user is set to `!` ,meaning the account is locked by default. **Use the **<mark style="color:red;">**`passwd`**</mark>** command to assign a password for the new user account**:

```bash
root@localhost:~ passwd test_user1                                              
Enter new UNIX password:                                                        
Retype new UNIX password:                                                       
passwd: password updated successfully
```

Also, notice from the first example that no options were used with the <mark style="color:red;">`useradd`</mark> command. The <mark style="color:red;">`useradd`</mark> command uses default options and settings from the **`/etc/login.defs`** file to create an account. **To view the default options used for account creation**, execute the <mark style="color:red;">**`useradd`**</mark> command with the <mark style="color:red;">**`-D`**</mark> option:

```bash
root@localhost:~ useradd -D                                                    
GROUP=100                                                                       
HOME=/home                                                                      
INACTIVE=-1                                                                     
EXPIRE=                                                                         
SHELL=/bin/bash                                                                 
SKEL=/etc/skel                                                                  
CREATE_MAIL_SPOOL=yes
```

**These default options are defined in the **<mark style="color:orange;">**`/etc/default/useradd`**</mark>** file**. The values in this file can be edited directly by `root` or set executing the <mark style="color:red;">`useradd`</mark> command, which updates the appropriate variable in `/etc/default/useradd`. The following example sets the login shell to `/bin/bash` for new user accounts:

```bash
root@localhost:~ useradd -D -s /bin/bash                                                    
GROUP=100                                                                       
HOME=/home                                                                      
INACTIVE=-1                                                                     
EXPIRE=                                                                         
SHELL=/bin/bash                                                                   
SKEL=/etc/skel                                                                  
CREATE_MAIL_SPOOL=no
```

The <mark style="color:red;">`useradd`</mark> command, by itself, creates a new account using default options, as described above. To create a new user with options different than the defaults, use the <mark style="color:red;">`useradd`</mark> command with option parameters:

```
useradd  -s <Shell> -d <Home directory> -m  -k <Skeleton directory> -g <Group> username
```

| Option | Meaning                                                                                                                                                                                                                               |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-s`   | <p>User’s default login shell.</p><p>The default on most Linux systems is<code>/bin/bash</code>. Other popular alternatives are: <code>/bin/tsh</code>, <code>/bin/dash</code>, <code>/bin/zsh</code>, and <code>/bin/ksh</code>.</p> |
| `-d`   | <p>The home directory for the new user.</p><p>The default on most Linux systems is<code>/home/username</code>.</p>                                                                                                                    |
| `-m`   | Creates the home directory for the user if it does not exist.                                                                                                                                                                         |
| `-k`   | <p>Copy initialization files from an alternative directory.</p><p>The default is <code>/etc/skel</code>.</p>                                                                                                                          |
| `-g`   | <p>Group name or number of the user.</p><p>The default group varies depending on the Linux distribution.</p>                                                                                                                          |
| `-N`   | <p>Avoids creation of a group with the same name as the user name.</p><p>An alternative group should be specified with the <code>–g</code> option.</p>                                                                                |

```bash
root@localhost:~ useradd -s /bin/bash -d /home/qa_user1 -m -g team  qa_user1  
root@localhost:~ tail -1 /etc/passwd                                           
qa_user1:x:1006:1004::/home/qa_user1:/bin/bash 
root@localhost:~ tail -1 /etc/shadow                                           
qa_user1:!:18162:0:99999:7::: 
root@localhost:~ grep team /etc/group                                          
team:x:1004:
```

### User Private Groups

Many Linux distributions, including Red Hat and Debian/Ubuntu, utilize User Private Groups (UPGs). When a new user is created with the <mark style="color:red;">`useradd`</mark> command, a group with the same name is also created. The new user is added as the only member of this private group.

```bash
root@localhost:~ useradd test_user2  
root@localhost:~ grep test_user2 /etc/passwd
test_user2:x:1007:1009::/home/test_user2:/bin/bash
root@localhost:~ tail -1 /etc/group
test_user2:x:1009:
```

The above output shows the UID created for `test_user2` is `1007` and the primary group create is `test_user2` with the GID of `1009.`

The UPG feature can be overridden by executing the <mark style="color:red;">`useradd`</mark> command with the <mark style="color:red;">`-N`</mark> and <mark style="color:red;">`-g`</mark> options, so that instead of a private group, another primary group can be associated with the new user.

The purpose of UPG is often confused, even by experienced administrators. Prior to UPG, it was a common practice for administrators to place all new users into a default group called `staff` (or `users` on some Linux distributions). This led to problems regarding file security because, by having everyone in the same group, the group permissions were essentially the same as the others permission set. In other words, a file with the `rwxrw----` permissions provided read and write permissions to every user on the system in cases where all users belonged to a common group.

UPG was designed to counter this problem. **By giving all users their own private group by default, the group permissions only applied to one person**: the owner of the file. However, this has the unintended side effect of regular users getting into the bad habit of changing the others permissions to provide access to a file for one person, which in turn gives access to everyone! As with the initial problem of having everyone in a single group, this essentially makes group permissions worthless.

{% hint style="info" %}
If you decide to use UPG, then consider making each user an administrator of their own group. This can be accomplished by executing the <mark style="color:red;">**`gpasswd -A`**</mark> command as `root`.

For example, to make the `test_user1` account a group administrator of the `test_user1` group, execute the following:

```bash
root@localhost:~ gpasswd -A test_user1 test_user1
```



After a user has been assigned to be a group administrator, they can add new members to their own group with the <mark style="color:red;">`-a`</mark> option to the <mark style="color:red;">`gpasswd`</mark> command:

```bash
root@localhost:~ gpasswd -A test_user2 test_user2    
root@localhost:~ su - test_user2                          
test_user2@localhost:~$ gpasswd -a sysadmin test_user2                                
Adding user sysadmin to group test_user2                                        
test_user2@localhost:~$ grep test_user2 /etc/group                                    
test_user2:x:1009:sysadmin
```
{% endhint %}

### Template Initialization Files for New Users

























































