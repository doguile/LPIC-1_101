# Module 11: User and System Administration

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

#### <mark style="background-color:orange;">The /etc/passwd File</mark>

The **`/etc/passwd`** file **contains the details of special system accounts used to run services**, and user accounts on the system. Without an entry in the **`/etc/passwd`** file, users are unable to log in to the system. **This file has read access for all users, but write access is limited to the root user.**

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

#### <mark style="background-color:orange;">The /etc/shadow File</mark>

The `/etc/shadow` file **contains the encrypted password of the user and some parameters related to password security**. In addition to providing the administrator with the ability to set password expiration fields.

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

{% hint style="info" %}
<mark style="color:orange;">`/etc/shadow`</mark>

File that store the cipher passwords of every use and information regarding its duration

```bash
sysadmin:$6$8.BhF.gs$gVnGSxU/Nvh0p14UrmL81:18010:0:99999:7:::
```

1. Usarname
2. Cipher Password with format `$id$salt$hashed`. The `$id` indicates the cipher algorith: `$1` MD5, `$2a$` o `$2y$` Blowfish, `$5$` SHA-256, `$6$` SHA-512
3. Save when was change it for the last time the password (number of days from 01/01/1970)
4. Minimum days that need to pass until it can be changed
5. Maximum days of validity of the password
6. Number of days during the user will be warned that it has to change the password
7. Days that will pass since the key expires until the account is disabled
{% endhint %}

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

The `/etc/skel` directory **contains the default set of "skeleton" (template) files and directories**, which are copied to the home directory of a user that is created with the <mark style="color:red;">`useradd`</mark> command. This **provides a uniform directory structure for all new users of the system**. If the adminstator wnats to make a change in the directory structure or file content, then the `/etc/skel` directory ensures that the change will be propagated for all new users.

Important notes:

* The **`/etc/skel`** directory is **only used to propagate accounts when an account is created**. If you add files to the `/etc/skel` directory, then these files are not automatically added to existing user's home directories.
* After files are copied into the user's home directories, **the ownership of these new files is changed to the new user's accounts**. This means that a user can modify the contents of these files, since they are owned by the user.
* Some of the commonly found files in this directory are:
  * `.bashrc` - contains alias definitions, enables shell features, etc
  * `.profile` - contains variable and one-time-only commands to set the user's environment
  * `bash_logout` - contains actions to take when logging out

Typically, **an administrator will use the **<mark style="color:orange;">**`/etc/skel`**</mark>** directory to generate a custom environment for new user accounts**. For example, to have the Firefox web browser contain default bookmarks for each new user, take the following steps:

1. Create a temporary user account that will be used to generate the Firefox bookmarks.
2. Log into the temporary user account
3. Launch Firefox and establish the bookmarks.
4. Log out of the temporary user account
5. Copy the `.mozilla` directory structure from the temporary user account's home directory to the `/etc/skel` directory.

As a result of the previous steps, all new user accounts would have a `.mozilla` directory automatically created in their home directory. When the Firefox web browser is launched, these bookmarks will be available.

It's also possible to create different `skel` directories for different types of user accounts. For example, consider a situation that requires different customization files for three groups of users: `developers`, `sales`, and `admin`. Create three different directories (for example `/etc/skel_dev`, `/etc/skel_sales`, and `/etc/skel_admin`) and populate each directory with different customization files. Then **specifies the template directory with the **_**skel**_**  **<mark style="color:red;">**`-k`**</mark>** option** (must be used with the _**create home**_ <mark style="color:red;">**`-m`**</mark> **option**) to the <mark style="color:red;">`useradd`</mark> command. The example below creates the new user `dan` with the directory structure copied from `/etc/skel_dev`

```bash
root@localhost:~ useradd -m -k /etc/skel_dev dan
```

### Updating User Passwords

**The **<mark style="color:red;">**`passwd`**</mark>** command is used to update a user's password**. Users can only change their own passwords, whereas the root user can update the password for any user.

If the root user wants to change the password for `sysadmin` ,then they would execute the following command:

```bash
root@localhost:~ passwd sysadmin                                               
Enter new UNIX password:                                                        
Retype new UNIX password:                                                       
passwd: password updated successfully
```

If the **user wants to view the status information about their password**, then they can execute the <mark style="color:red;">**`-S`**</mark> option to the <mark style="color:red;">**`passwd`**</mark> command:

```bash
sysadmin@localhost:~$ passwd -S sysadmin
sysadmin P 04/24/2019  0 99999 7 -1
```

The output fields are explained below:

| Field                     | Example      | Meaning                                                                                                                        |
| ------------------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| User Name                 | `sysadmin`   | Name of the user                                                                                                               |
| Password status           | `P`          | <p><code>P</code> means usable password</p><p><code>L</code> means locked password</p><p><code>NP</code> means no password</p> |
| Last password change date | `03/01/2020` | Date when the password was last changed                                                                                        |
| Minimum                   | `0`          | Minimum number of days that must pass before the current password can be changed by the user                                   |
| Maximum                   | `99999`      | Maximum number of days remaining for the password to expire                                                                    |
| Warn                      | `7`          | Number of days prior to password expiry that the user is warned                                                                |
| Inactive                  | `-1`         | Number of days after password expiry that the user account remains active                                                      |

The **root user can enforce a password change** in the next login attempt by executing the <mark style="color:red;">**`-e`**</mark> option to the <mark style="color:red;">**`passwd`**</mark> command

```bash
root@localhost:~ passwd -e sysadmin                                               
passwd: password expiry information changed.
```

This will cause the existing password of the user `sysadmin` to expire and force the user to provide a new password during the next login.

### New User Password Aging Defaults

The `/etc/login.defs` file contains parameters that define the defaults values used when new Linux user accounts are created, for items such as home directory location, new file permissions, password aging controls, etc. **This is a plain text file with one configuration parameter on each line**. There are three types of parameters: boolean (true or false), number, and string. The three parameters that set the default password aging control are described in the table below.

| Parameter       | Type   | Meaning                                                                                                               |
| --------------- | ------ | --------------------------------------------------------------------------------------------------------------------- |
| `PASS_MAX_DAYS` | number | <p>Maximum number of days a password is valid</p><p>A value of <code>99999</code> means “no maximum password age”</p> |
| `PASS_MIN_DAYS` | number | <p>Minimum number of days a password is valid</p><p>A value of <code>0</code> means “no minimum password age”</p>     |
| `PASS_WARN_AGE` | number | Number of days before password expiry that a warning message is given                                                 |

{% hint style="info" %}
Modifying the password aging or any parameter in the `/etc/login.defs` file only affects new user accounts. **Use the **<mark style="color:red;">**`chage`**</mark>** (change aging) command to modify password aging information for existing accounts**.
{% endhint %}

### Locking User Accounts

The root user can lock and unlock a user's account by using the <mark style="color:red;">**`passwd -l username`**</mark> to lock a username and <mark style="color:red;">**`passwd -u username`**</mark> command to unlock the username, repectively.

When an account is locked, the password entry in the `/etc/shadow` file is made invalid by prepending a <mark style="color:red;">`!`</mark> or <mark style="color:red;">`!!`</mark> to the encrypted password. To unlock the entry, the `!` or `!!` characters are removed.

```bash
root@localhost:~ grep sysadmin /etc/shadow                                     
sysadmin:$6$8.BhF.gs$gVnGSxU/JM4mcKgdbvgIOPWGT5XN25cZwEyI4W3/Nvh0p14UrUrSuvqp3SpPBBq9xYGpmbCAYPHfLgYLpnmL81:18010:0:99999:7:::                                       

root@localhost:~ passwd -l sysadmin                                            
passwd: password expiry information changed.

root@localhost:~ grep sysadmin /etc/shadow                                     
sysadmin:!$6$8.BhF.gs$gVnGSxU/JM4mcKgdbvgIOPWGT5XN25cZwEyI4W3/Nvh0p14UrUrSuvqp3SpPBBq9xYGpmbCAYPHfLgYLpnmL81:18010:0:99999:7:::                                     
‌⁠​​⁠
root@localhost:~ passwd -u sysadmin                                            
passwd: password expiry information changed.

root@localhost:~ grep sysadmin /etc/shadow                                     
sysadmin:$6$8.BhF.gs$gVnGSxU/JM4mcKgdbvgIOPWGT5XN25cZwEyI4W3/Nvh0p14UrUrSuvqp3SpPBBq9xYGpmbCAYPHfLgYLpnmL81:18010:0:99999:7:::
```

The root user can **remove the encrypted password entry for a user** account by running the <mark style="color:red;">**`-d`**</mark> option to the <mark style="color:red;">`passwd`</mark> command.

```bash
root@localhost:~ passwd -d sysadmin                                            
passwd: password expiry information changed.
root@localhost:~ grep sysadmin /etc/shadow                                     
sysadmin::18010:0:99999:7:::
```

### Password Policy

The <mark style="color:red;">**`chage`**</mark> command is used to **update the information related to passwod expiration**. Using this command, **the administrator can enforce a password changing and expiry policy** for specific user accounts.

This command can be run by the root user to modify user accounts; regular user cna use a view-only option.

```bash
sysadmin@localhost:~$ chage -l sysadmin                                         
Last password change                                    : Apr 24, 2019          
Password expires                                        : never                 
Password inactive                                       : never                 
Account expires                                         : never                 
Minimum number of days between password change          : 0                     
Maximum number of days between password change          : 99999                 
Number of days of warning before password expires       : 7    
```

#### <mark style="background-color:orange;">Force Users to change passwords periodically</mark>

The <mark style="color:red;">`chage`</mark> command can be used to **change the number of days between when a user creates a new password and when the user is required to change the password again**. For example, to force `test_user` to change their password within 90 days from when it was last changed (otherwise the password will _expire_ ), execue the <mark style="color:red;">`chage`</mark> command with the <mark style="color:red;">`-M`</mark> option.

```bash
root@localhost:~ chage -M 90 test_user1                                         
root@localhost:~ !grep                                                         
grep test_user1 /etc/shadow                                                      
test_user1:$6$VBdpqLwC$.HwlyAvpTbvxT0ruyFULvWb/1:18163:0:90:7:::
```

{% hint style="info" %}
By using the <mark style="color:red;">**`–m`**</mark> option to the <mark style="color:red;">`chage`</mark> command, **it is possible to specify a minimum number of days** (fourth field in `/etc/shadow`; `default=0`) that the **user will have to wait before being allowed to change their password** and thus prevent them from immediately changing it back to their previous password:

```bash
root@localhost:~ chage -m 5 test_user                                         
root@localhost:~ !grep                                                         
grep test_user /etc/shadow                                                      
test_user1:$6$VBdpqHivxT0ruyFULvWb/1:18163:5:90:7:::
```

****

**PAM** (Pluggable Authentication Module) provides a method to have Linux remember previously used passwords, forcing users to create a new passwords each time they are forced to change their password
{% endhint %}

#### <mark style="background-color:orange;">**Providing Warnings and Allowing Grace Login**</mark>

If a user has a maximum password age limit of 90 days and they attempt to log in 91 days after the last time the account password was changed, the account will automatically be locked. The sixth (warning) field in the `/etc/shadow` file allows administrators to specify how many days before the account will be locked that the user will be warned of the potential lock-out

Note that this warning only appears when the user logs in:

```bash
Login: test_user1                                                      
Password:                                                                       
Warning:  your password will expire in 7 days
```

To **set the warning field** in the `/etc/shadow` file for a user, use the <mark style="color:red;">`-W`</mark> option to the <mark style="color:red;">`chage`</mark> command:

```bash
root@localhost:~ chage -W 3 test_user1
root@localhost:~ grep test_user1 /etc/shadow                                    
test_user1:$6$VBdpqLwC$.HwlyAvpb28M.vBjcfYcHivxT0ruyFULvWb/1:18163:5:90:3:::
```

When the user fails to heed the warnings, they will be locked out of their account, requiring a system administrator to unlock it. **To provide the user with a bit more time to change the password**, a grace login period can be permitted with the <mark style="color:red;">`-I`</mark> (inactive) option to the <mark style="color:red;">`chage`</mark> command:

```bash
root@localhost:~ chage -I 10 test_user1
root@localhost:~ grep test_user1 /etc/shadow                                    
test_user1:$6$gLAMp5rD$.lh2mLPolR0ZzRZXlBwiJ0CgxeZ1:16468:0:90:3:10::
```

The preceding command sets the grace login period to `10` days. This means for the 10-day period after the maximum password age limit has been reached, the **user can log in and will be force to change their password** during the login process.

#### <mark style="background-color:orange;">Set an Expiration Date</mark>

To **set the expiration for a user account** to a specific date, use the <mark style="color:red;">`chage`</mark> command with the <mark style="color:red;">`-E`</mark> (Expiration) option, followed by the date in the YYYY-MM-DD format (or the format of your location)

```bash
root@localhost:~ chage -E “2025-04-15” test_user1
root@localhost:~ grep test_user1 /etc/shadow                                    
test_user1:$6$VBdpqLwC$.HwltbvxT0ruyFULvWb/1:18163:5:90:3::20193:
```

The command above will update the eight field of the `/etc/shadow` file for the `test_user` account to `20193` days from 1 January, 1970, which in this case is 15 April, 2025; the day the account will be locked.

{% hint style="info" %}
UNIX and Linux systems have historically kept track of time as a value since January 1, 1970, measuring it from that date until now.
{% endhint %}

#### <mark style="background-color:orange;">Viewing Password Aging Policies</mark>

Any user can **display the password aging policies** for their own account by executing the        <mark style="color:red;">**`chage -l sysadmin`**</mark> command. For example

```bash
sysadmin@localhost:~$ chage -l sysadmin                                         
Last password change                                : Apr 24, 2019          
Password expires                                    : never                 
Password inactive                                   : never                 
Account expires                                     : never                 
Minimum number of days between password change      : 0                     
Maximum number of days between password change      : 99999                 
Number of days of warning before password expires   : 7
```

The administrator can use this command for any user account. This is especially useful for translating the expiration field into an actual date.

### Modifying a User

Periodically, an administrator receives a request to modify user account parameters to accommodate a job-role change, a system change, or to simply update user information in the comment field of the `passwd` file. Experienced administrator can make direct changes to the `/etc/passwd` and `/etc/shadow` files, but it is **best to use the **<mark style="color:red;">**`usermod`**</mark>** (user modification)** command.

The <mark style="color:red;">`usermod`</mark> command is **used by the root account to modify existing user account parameters** such as home directory, login name, login shell, password aging information, etc. Verify all changes with the `grep` command.

To **change the home directory location** of the user `test_user1` from `/home/test_user1` to `/home/qa/test_user1` ,execute the <mark style="color:red;">**`usermod`**</mark> command with the <mark style="color:red;">**`-d`**</mark> option:

```bash
root@localhost:~ mkdir -p /home/qa                                           
root@localhost:~ usermod -d /home/qa/test_user1 test_user1                       
root@localhost:~ grep test_user1 /etc/passwd                                   
test_user1:x:1005:1008::/home/qa/test_user1:/bin/bash
```

{% hint style="warning" %}
The <mark style="color:red;">`useradd`</mark> command only changes the home directory field in the `/etc/passwd`
{% endhint %}

**To lock** (insert an exclamation point <mark style="color:red;">`!`</mark> in front of the encrypted password in `/etc/shadow`) and **unlock** (remove the exclamation point <mark style="color:red;">`!`</mark>) the password for user `test_user1` ,use the <mark style="color:red;">`usermod`</mark> command with the <mark style="color:red;">**`-L`**</mark> and <mark style="color:red;">**`-U`**</mark> options respectively:

```bash
root@localhost:~ usermod -L test_user1
root@localhost:~ grep test_user1 /etc/shadow                                    
test_user1:!$6$VBdpqLwC$.HwlyAvpb28M..cNnCTbtbr1/./1:18163:5:90:3::20193:     
root@localhost:~ usermod -U test_user1
```

To **set the expiry date** of `test_user1` to April 30, 2025, use the <mark style="color:red;">**`-e`**</mark> option (note the different format than the <mark style="color:red;">`chage`</mark> command)

```bash
root@localhost:~ usermod test_user1 -e 2025-04-30
root@localhost:~ grep test_user /etc/shadow                                    
test_user1:!$6$VBdpqLwC$.HwlyAvpb28M/1:18163:5:90:3::20208:
```

**To change** the `test_user1` **primary group** to the `team` group, use the <mark style="color:red;">**`-g`**</mark> option.

```bash
root@localhost:~ usermod test_user1 -g team
```

To **append the accounts group to the list of existing supplementary/secondary groups** for `test_user1`, use both the <mark style="color:red;">**`-a`**</mark> (append) and <mark style="color:red;">**`-G`**</mark>(group) options.

```bash
root@localhost:~ usermod -a -G accounts test_user1
```

{% hint style="info" %}
Use the <mark style="color:red;">`-G`</mark> option (without the <mark style="color:red;">`-a`</mark> option) to list, separated by a comma, all supplementary/secondary groups that the user is a member of. If the user is currently a member of a group that is not listed, the user will removed from the group
{% endhint %}

To **change the comment** (or GECOS field) in the `/etc/passwd` file, use the <mark style="color:red;">**`-c`**</mark> option:

```bash
root@localhost:~ usermod test_user1 -c "engineering team"
root@localhost:~ grep test_user1 /etc/passwd                                   
test_user1:x:1005:1004:engineering team:/home/qa/test_user1:/bin/bash
```

To **change the login shell** of `test_user1` to the C shell, use the <mark style="color:red;">**`-s`**</mark> option:

```bash
root@localhost:~ usermod test_user1 -s /bin/csh
```

On Red Hat-based system, the <mark style="color:red;">`usermod`</mark> command verifies that the user is not logged in before changing attributes such as the user ID and home directory.

### Deleting a User

The <mark style="color:red;">`userdel`</mark> command is used to **delete a user account information**, preventing the user from logging in and optionally removing all files related to the account.

The home directory of the user, including all files and directories created by the user, will not be deleted but can be searched and later removed using the `find` command.

To **delete the user account along with the user's home directory** and mail spool (file containing user's unread mail), use the <mark style="color:red;">**`-r`**</mark> **option**

```bash
root@localhost:~ userdel -r test_user1
```

{% hint style="danger" %}
The <mark style="color:red;">`-r`</mark> option only deletes the user's home directory and mail spool; any other files owned by the user in other directories must be deleted manually.
{% endhint %}

In the event an active employee is terminated immediately, **the user account can be forcefully deleted even if the user is logged in by using the **<mark style="color:red;">**`-f`**</mark>** option**.

```bash
root@localhost:~ userdel -f test_user1
```

## Group Accounts

Users are organized in groups to facilitate the management, monitoring, and control of users and resources. Below are two examples of using a group:

* The root user can create an `admin` group, which can be configured to allow group members the rights to execute administration-based tasks.
* When a server is being shared by multiple teams in an organization, a separate group can be created for each team. Their shared resources, such as files and directories, will be accessed securely by team members only.

**Every user is associated with at least one group know their primary group**. Recall that the user's primary group is stored in the fourth field of the `/etc/passwd` file. Any additional group membership will be indicated in the `/etc/group` file, which is used to store group account information.&#x20;

### The `/etc/group` File

The `/etc/group` file is a text file that defines the groups to which a user belongs, plus various system groups automatically created as part of the Linux installation.

```bash
sysadmin@localhost:~$ head /etc/group                                           
root:x:0:                                                                       
daemon:x:1:                                                                     
bin:x:2:                                                                        
sys:x:3:                                                                        
adm:x:4:syslog,sysadmin                                                         
tty:x:5:                                                                        
disk:x:6:                                                                       
lp:x:7:        
```

Each record in the `/etc/group` files contains the following four field, separated by colons:

```
group_name:password:GID:group_list
```

| Field      | Example                       | Significance                                                                                                                                                                                                                                                                                                                                                    |
| ---------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Group Name | `marketing`                   | Name of the group                                                                                                                                                                                                                                                                                                                                               |
| Password   |                               | <p>This field is blank for most of the groups</p><p>Traditionally, it can store an encrypted password for privileged groups, although most modern distributions of Linux stores group passwords in the <code>/etc/gshadow</code> file</p><p>An <code>x</code> in this field indicates there might be a group password in the <code>/etc/gshadow</code> file</p> |
| GID        | `1002`                        | The Group ID                                                                                                                                                                                                                                                                                                                                                    |
| Group List | `Madeleine,Colin,Condoleezza` | List of the user ids who are members of this group                                                                                                                                                                                                                                                                                                              |

A user can be a member of multiple groups, but at any point in time, only one group will be the user's primary group. **The user's primary group is used to determine what group will be given ownership of a file when the user creates a new file**.

To check the current group memebership of a particular user, use the <mark style="color:red;">`groups`</mark> command.

```bash
sysadmin@localhost:~$ groups                                                    
sysadmin adm sudo
```

To see the group membership of a different user account, specify the account name as an argument to the `groups` command:

```bash
sysadmin@localhost:~$ groups root                                               
root : root
```

Another method to determine a user's default primary group is the <mark style="color:red;">`getent`</mark> (get entries) command. **The **<mark style="color:red;">**`getent`**</mark>** command is an easy way to query administrative files** (`passwd`, `shadow`, `groups`, `hosts`, etc) called databases that store user information. The command syntax is:

```
getent database [optional key]
```

```bash
sysadmin@localhost:~$ getent group sysadmin                                     
sysadmin:x:1001:
```

Notice that the primary group is also the first one listed when executing the <mark style="color:red;">`groups`</mark> command. However, keep in mind that a user can temporarily change their primary group with the <mark style="color:red;">`newgrp`</mark> command. The <mark style="color:red;">`newgrp`</mark> command will open a new shell with the group specified as the primary group.

```bash
sysadmin@localhost:~$ groups                                                    
sysadmin adm sudo                                                               
sysadmin@localhost:~$ newgrp adm                                                
sysadmin@localhost:~$ groups                                                    
adm sudo sysadmin
```

The difference between the output from the <mark style="color:red;">`groups`</mark> command and the <mark style="color:red;">`getent`</mark> command is that **the **<mark style="color:red;">**`groups`**</mark>** command displays the user's current primary group** as the first group while **the **<mark style="color:red;">**`getent`**</mark>** command will always display the default primary group** for the user's account.

The <mark style="color:red;">`groups`</mark> command also list all of the secondary groups for the user, while the <mark style="color:red;">`getent`</mark> command only displays the user's primary group.

### Creating a new group

The <mark style="color:red;">`groupadd`</mark> command is **used to create a new group**. For example, to add a new group named `programmers`, execute the following command as root:

```bash
sysadmin@localhost:~$ sudo groupadd programmers
root@localhost:~ tail -1 /etc/group
programmers:x:1008:
```

By default, the <mark style="color:red;">**`groupadd`**</mark>** ** command automatically assigns a GID to the new group using the next available GID. **To create a group with a specific GID, use the **<mark style="color:red;">**`-g`**</mark>** option**:

```bash
root@localhost:~ groupadd programmers -g 1980
programmers:x:1980:
```

### Modifying a Group

The <mark style="color:red;">**`groupmod`**</mark> command is used to **modify the properties of an existing group account**. For example, **to modify the group's GID, use the **<mark style="color:red;">**`-g`**</mark>** option**

```
root@localhost:~ groupmod programmers -g 1990                    
root@localhost:~ tail -1 /etc/group
programmers:x:1990:
```

To change the group name, execute the `groupmod` with the `-n` option:

```
root@localhost:~ groupmod programmers -n programmers2                       
root@localhost:~ tail -1 /etc/group
programmers2:x:1990:
```

{% hint style="info" %}
Group level passwords may be assigned, and these passwords are stored in the **`/etc/gshadow`** file. These passwords affect the ability of users to execute the <mark style="color:red;">`newgrp`</mark> command used to switch to a different primary group.
{% endhint %}

Similar to the `/etc/shadow` file, the `/etc/gshadow` file is used to store group password information and other security-related group data. **If a group is added, deleted, or modified, then the `/etc/gshadow` file is also updated.**

```bash
root@localhost:~ tail /etc/gshadow                                     
colord:!::                                                                      
bind:!::                                                                        
mysql:!::                                                                       
smmta:!::                                                                       
smmsp:!::                                                                       
ssh:!::                                                                         
ntpd:!::                                                                        
sysadmin:!:: 
userdmin:!::                                                                                                                                   
programmers:$1$nfffgggc$pGt4:useradmin:sarah,mickey,dinesh
```

Each record in the /etc/gshadow file contains the following 4 fields, separated by colons:

```
group_name:encrypted_password:group_administrators:members
```

| Field                | Example               | Significance                                                                                                                                                           |
| -------------------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Group Name           | `programmers`         | Valid existing group name                                                                                                                                              |
| Encrypted Password   | `$1$nfffgggc$pGt#4`   | Used when a user who is not a member of the group wants to gain permission to this group. An exclamation mark at the beginning of the password indicates it is locked. |
| Group Administrators | `useradmin`           | Comma-separated list of group administrators                                                                                                                           |
| Members              | `sarah,mickey,dinesh` | Comma-separated list of group users that will not be prompted for a password                                                                                           |

The <mark style="color:red;">**`gpasswd`**</mark> command is used by the root user to **update the group information** in the `/etc/gshadow` file. For example, to give administrative rights to user `sysadmin` for group `programmers` ,execute the following

```bash
root@localhost:~ gpasswd -A sysadmin programmers
root@localhost:~ tail -1 /etc/gshadow                                                                                                                                                         
programmers:$1$nfffgggc$pGt4:sysadmin:
```

To set a group password, execute the `gpasswd` command with no options

```
root@localhost:~# gpasswd programmers
Changing the password for group programmers
New Password:
Re-enter new password:
```

To **remove a group password**, execute the <mark style="color:red;">`gpasswd`</mark> command with the <mark style="color:red;">**`-r`**</mark> **option**:

```bash
root@localhost:~ gpasswd -r programmers
```

### Deleting a Group

The <mark style="color:red;">**`groupdel`**</mark> command is **used to delete a group that is no longer needed**. For example to delete the group named `programmers` ,execute the following

```
root@localhost:~# groupdel programmers
```

An error message is displayed when the group being deleted is the primary group of any existing user:

```bash
root@localhost:~ groupdel sysadmin
groupdel:  cannot remove the primary group of user ‘sysadmin’
```

The user needs to be deleted first or reassigned to a different group before the group can be deleted.

{% hint style="info" %}
Keep in mind that deleting a group that owns files could cause access problems.

In reality, **files are owned by GID numbers, not by groups**.&#x20;
{% endhint %}

## Using Pluggable Authentication Modules

Many Linux distributions use the Pluggable Authentication Modules (PAM) framework for implementing security and authentication. Authentication is the process of ensuring user accounts are valid and that credentials provided by the user are valid.

There are many applications running on the system which need to use an authentication mechanism to check the credentials of the users trying to access resources or services. Using PAM allows a uniform authentication method for application access and also facilitates a standard configuration.

The module can be plugged into the system without the need for any recompilation. PAM considers the following aspects for providing secure access:

1. Authentication
2. Managing sessions
3. Managing accounts
4. Updating authentication information
