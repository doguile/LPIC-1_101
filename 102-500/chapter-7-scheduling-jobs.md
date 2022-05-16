---
description: '107.2: Automate system administration tasks by scheduling jobs v2  Weight: 4'
---

# Chapter 7: Scheduling Jobs

<details>

<summary>Key terms</summary>

`/etc/at.allow` One of two files, /etc/at.allow being the other, maintained by the root user **used to restrict the use of the at and batch commands**. They should contain a list of users, with one user specified per line

`/etc/at.deny` One of two files, /etc/at.allow being the other, maintained by the root user **used to restrict the use of the at and batch commands**. They should contain a list of users, with one user specified per line

`/etc/cron.allow` One of two files, /etc/at.allow being the other, maintained by the root user **used to restrict the use of crontab commands**. They should contain a list of users, with one user specified per line

`/etc/cron.deny` One of two files, /etc/at.allow being the other, maintained by the root user **used to restrict the use of crontab commands**. They should contain a list of users, with one user specified per line

`/etc/cron.{d,daily,hourly,monthly,weekly}` Crontabs tasks require finer control of their scheduling and do not need environmental variables to be loaded before script execution are stored in /etc/cron.{d,daily,hourly,monthly,weekly} directory

<mark style="color:red;">`/etc/crontab`</mark> System crontab file. **Stores scheduling information for system wide tasks**

<mark style="color:red;">`/var/spool/cron/`</mark> Directory **containing user crontab files**

<mark style="color:red;">`at`</mark> Command **used to run one time tasks at a specified ti**me

<mark style="color:red;">`atq`</mark> Command that **lists the user's pending jobs.** If the command is being run as the root user, the it will list job for all the users

<mark style="color:red;">`atrm`</mark> Command **used to remove at job(s) in the `at` queue** awaiting execution

<mark style="color:red;">`crontab`</mark> Command **used to view and edit (`-e`) the system (if root) or a user's crontab file** (/var/spool/cron directory) that stores scheduled program information

<mark style="color:red;">`systemctl`</mark> The systemctl command is **used to interface with and control systemd units**

<mark style="color:red;">`systemd-run`</mark> The systemd-run command can be **used to run a command or execute a systemd `.service` unit**

</details>

## Introduction

Scheduling tasks or executing commands at a particular time is inherently part of Linux administration.

There are a wide variety of commands that can be executed at a future time. However, keep in mind that the command should be non-interactive since the assumption is that there is no user to interact with when the commands execute. Generally speaking, the 4 tools described in this chapter (<mark style="color:red;">`cron`</mark>, <mark style="color:red;">`at`</mark>, <mark style="color:red;">`batch`</mark>, and systemd timers) will be extremely useful for scheduling tasks for execution at some later time.

## Understanding cron

The <mark style="color:red;">`cron`</mark> command derives its name from chronos, the Greek word for time. <mark style="color:red;">`cron`</mark> is a utility that provides tools, including:

* A time-based job scheduler that runs as a daemon named <mark style="color:red;">**`crond`**</mark> .This daemon is used to **execute repetitive tasks** at a predetermined interval
* A <mark style="color:red;">`crontab`</mark> (cron table) file that **stores scheduled tasks** (commonly referred to as _jobs_ or _cron jobs_) **to execute and is read by the **<mark style="color:red;">**`crond`**</mark>** daemon**. Each user is permitted to have a personal `crontab` file by default, but the admin can disable this feature for security purposes
* The <mark style="color:red;">`crontab`</mark> command **used to view and edit a user's `crontab` file** (which is initially empty)

## System crontab and User crontab

The system <mark style="color:red;">`crontab`</mark> allows scheduling of system-wide taks such as log rotations, backups and system database updates. Modifying the system <mark style="color:red;">`crontab`</mark> for scheduling task requires administrative privileges.

{% hint style="info" %}
Additional system `crontab` jobs that require finer control of their scheduling and do not need environmental variables to be loaded before script execution are stored in the `/etc/cron.d` directory
{% endhint %}

The system <mark style="color:red;">`crontab`</mark> can execute commands either as root or as any other user. For example, if a user does not have access to <mark style="color:red;">`cron`</mark>, but needs to have a particular script run at the end of the day on a daily basis, then they can ask the administrator to add an entry in the system <mark style="color:red;">`crontab`</mark>.

The following is an example of a typical system <mark style="color:red;">`crontab`</mark> file:

```bash
# /etc/crontab - root's crontab
#
#2014-03-20 17:14:40 
# 
#
SHELL=/bin/sh
PATH=/etc:/bin:/sbin:/usr/bin:/usr/sbin 
#
#minute hour mday month wday who command 
#
0 23 * * * root /usr/root/bkup.sh
*/30 * * * * dev /usr/dev/ant_build.sh
```

The first ten lines show comments and set variables (`SHELL`, `PATH`) that define the environment for the scheduled jobs when they run. The last two lines describe what command or script is executed. Each of these lines describes one command to execute. Based on the two entries, the first command will be executed as the `root` user and the second command will be executed as the `dev` user.

In addition to the system <mark style="color:red;">`crontab`</mark> maintained by root, **each user can have a personal **<mark style="color:red;">**`crontab`**</mark> **file** unless prevented by the administrator. Like the system <mark style="color:red;">`crontab`</mark> file, these commands will run regardless of wheter the user is logged into the system

The following is an example of a basic user <mark style="color:red;">`crontab`</mark> file:

```
#minute hour mday month wday who command 
⁠⁠ 
#
0 17 * * 5 /usr/bin/banner  "The Weekend Is Here!" > /dev/console
```

The user <mark style="color:red;">`crontab`</mark> is similar to the system <mark style="color:red;">`crontab`</mark> except for the 6th field, which contains the user name. This field is not included in the user `crontab` since all the entries are for that particular user only.

## Understanding crontab Entries

The <mark style="color:red;">`crontab`</mark> entries can be used to schedule tasks that can occur as frequently as once every minute to as infrequently as once a year. The format of a `crontab` entry is:

```
Minute Hour Day-of-month Month Day-of-week command
```

| Field        | Values                               |
| ------------ | ------------------------------------ |
| Minute       | Range `0` – `59`                     |
| Hour         | Range `0` – `23`                     |
| Day-of-month | Range `1` – `31`                     |
| Month        | Range `1` – `12` (Jan-Dec)           |
| Day-of-week  | Range `0` – `7` (`0` & `7` = Sunday) |
| Command      | The command to be executed           |

The first five fields can contain the following, to represent time values:

* A single value
* Multiple values such as 1,3,5 in the fifth field, which would mean Monday, Wednesday, Friday
* A range of values (such as 1 through 5 (1-5)) in the fifth field
* An asterisk `*` character means any or all values

#### Example crontab Entries

**Example 1**

```
30 04 1 1 1 /usr/bin/some_command
```

The above entry will run at 4:30 am on January 1st, plus every Monday in January

**Example 2**

```bash
30 04 * * * /usr/bin/some_command
```

The above entry will run at 4:30am on every day of every mont

**Example 3**

```bash
01,31 04,05 1-15 1,6 * /usr/bin/some_command
```

**Comma-separated values can be used to run more than one instance** of a particular command with a time period. Dash-separated values can be used to run a command continuosly. The above entry will run at 01 and 31 past the hours of 4:00 am and 5:00 am on the 1st through the 15th of every January and June

**Example 4**

```bash
00 08-17 * * 1-5 /usr/bin/some_command
```

The above entry will run every hour (on the hour) from 8AM to 5PM, Monday through Friday of every Month.

### Variables in crontabs

The `crontab` file can also contain variables; some of the most commonly-found variables are:

```bash
PATH=/usr/local/bin:/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin
SHELL=/bin/bash
MAILTO=databaseadmin
```

The <mark style="color:red;">`PATH`</mark> variable is very important in the event that relative pathnames are used when specifying commnads.

It is important to note that the <mark style="color:red;">`crond`</mark> utility does not read user initializations files when it executes commands. This means that creating variables like the `PATH` variable is very important

The <mark style="color:red;">`MAILTO`</mark> variable is important for any command that produces output when executed. There is no assumption that the user is logged in when the command runs, so the output of the command is sent to the user's email address by default.

### Crontab Keywords

As defined in the <mark style="color:red;">`crontab`</mark> man page, the following special keywords can also be used to specify a time in place of the normal five fields representing time values:

| Entry                                                    | Description          | Equivalent to |
| -------------------------------------------------------- | -------------------- | ------------- |
| `@reboot`                                                | Run once, at startup |               |
| <p><code>@yearly</code></p><p><code>@annually</code></p> | Run once a year      | `0 0 1 1 *`   |
| `@monthly`                                               | Run once a month     | `0 0 1 * *`   |
| `@weekly`                                                | Run once a week      | `0 0 * * 0`   |
| <p><code>@daily</code></p><p><code>@midnight</code></p>  | Run once a day       | `0 0 * * *`   |
| `@hourly`                                                | Run once an hour     | `0 * * * *`   |

For example, the `crontab` entry to execute the `/home/sysadmin/bin/daily-backup` script at 00:00 (midnight) every day could be entered in one of three ways:

```
0 0 * * *  /home/sysadmin/bin/daily-backup
```

OR

```
@daily  /home/sysadmin/bin/daily-backup
```

OR

```
@midnight  /home/sysadmin/bin/daily-backup
```

## Maintaining User crontab Files

**The **<mark style="color:red;">**`crontab`**</mark>** command is used for maintaining user `crontab` files**. The user `crontab` files are **stored in the `/var/spool/cron` ** directory, but should not be edited directly.&#x20;

> In fact, regular users don't have any access to this directory, so a user must use the `crontab` command to display or edit his own `crontab` file. This also means that a regular user can't display `crontab` file of other users.

To edit the `crontab` file, a user can type the <mark style="color:red;">`crontab`</mark> command with the <mark style="color:red;">`-e`</mark> option:

```bash
sysadmin@localhost:~$ crontab -e
no crontab for sysadmin - using an empty one
```

The <mark style="color:red;">`crontab`</mark> command will start a text editor program to allow for modification of that user's `crontab` file.

```
# Edit this file to introduce tasks to be run by cron.                          
#                                                                               
# Each task to run has to be defined through a single line                      
# indicating with different fields when the task will be run                    
# and what command to run for the task                                          
#                                                                               
# To define the time you can provide concrete values for                        
# minute (m), hour (h), day of month (dom), month (mon),                        
# and day of week (dow) or use '*' in these fields (for 'any').#                
# Notice that tasks will be started based on the cron's system                  
# daemon's notion of time and timezones.                      
```

To specify a different editot, set the `EDITOR` variable before executing the <mark style="color:red;">`crontab -e`</mark> command. For example, to use the `gedit` editor (a graphical editor), use the <mark style="color:red;">`export EDITOR=gedit`</mark> command.

> The `crond` daemon will automatically update the `crontab` file that it stores in memory, so the changes take effect inmediately

One of the useful features of the <mark style="color:red;">`crontab`</mark> command is that it performs some basic error checking. For example, if an invalid time field is specified, the <mark style="color:red;">`crontab`</mark> command will issue a warning and provide the opportunity to fix the error.

```bash
sysadmin@localhost:~$ crontab -e                                                
no crontab for sysadmin - using an empty one                                    
crontab: installing new crontab                                                 
"/tmp/crontab.Dwt5Vg/crontab":22: bad month                                     
errors in crontab file, can't install.                                          
Do you want to retry the same edit? (y/n)
```

Each user can **view the current contents of their `crontab` file** using the <mark style="color:red;">**`crontab -l`**</mark> command. Each user can also **remove all of their current `crontab` ** entries, using the <mark style="color:red;">**`crontab -r`**</mark> command. **An administrator logged in as the root user can view or modify another user's `crontab` file** by specifying a username with the <mark style="color:red;">`-u`</mark> option: <mark style="color:red;">**`crontab -u sysadmin`**</mark>

The <mark style="color:red;">`crond`</mark> daemon looks for user `crontab` files in the specified spool area; typically, this is either the **`/var/spool/cron`** directory, but it may be the **`/var/spool/cron/crontabs`** directory on some distributions.

The `crontab` files found in these locations are loaded into memory for use by the <mark style="color:red;">`crond`</mark> daemon.

## Mainitaining System crontab Files

**The **<mark style="color:red;">**`crond`**</mark>** daemon read the system `crontab` from the **<mark style="color:orange;">**`/etc/crontab`**</mark>** file**. The contents of this file will vary, depending on the distribution being used.

It is important to note that on modern releases of Red Hat Enterprise Linux, the `/etc/crontab` file may contain no entries by default. On previous releases (and on some current distributions of Linux) there may be entries in this file that direct the `crond` daemon to execute commands in the following directories:

* `/etc/cron.hourly`
* `/etc/cron.daily`
* `/etc/cron.weekly`

Commands and executables in these directories are executed on an hourly, daily, or weekly basis.

**The **<mark style="color:red;">**`crond`**</mark>** daemon also reads the files in the **<mark style="color:orange;">**`/etc/cron.d`**</mark>** directory**. This location is used by various packages to exercise fine control when scheduling system tasks.

{% hint style="info" %}
A software application can create a `crontab` file and place it in the `/etc/cron.d` directory during installation ( or remove it from directory when the package is uninstalled from the system)
{% endhint %}

**The **<mark style="color:red;">**`crond`**</mark>** daemon** **keeps track of changes in `crontabs`** by checking the modification time of files. It will pool for changes in the modification time of all `crontabs` every minute and reload the changed files, if any. There is no need to restart the <mark style="color:red;">`crond`</mark> daemon if any of the `crontabs` have been updated.

The <mark style="color:red;">`crond`</mark> daemon can be started, stopped, and restarted by the root user using the following commands:

```bash
/etc/init.d/crond start
/etc/init.d/crond stop
/etc/init.d/cron restart
```

## Controlling Access to cron

The root user always has the ability to execute the <mark style="color:red;">`crontab`</mark> command, but this access can be restricted for other users. The two files used for this purpose are the <mark style="color:orange;">**`/etc/cron.allow`**</mark> file and the <mark style="color:orange;">**`/etc/cron.deny`**</mark> file. These files are created and maintained by the root user. **They should contain a list of users, with one user specified per line**. The following rules are used to determine how these files are used:

1. If both the `cron.allow` and the `cron.deny` files do not exist, the default on current Ubuntu and other Debiab-based system is to allow all users to use the `crontab` command.
2. If only the `cron.allow` file exists, then only the users listed in the file can execute the `crontab` command
3. If only the `cron.deny` file exists, then all users listed in this file are denied the ability to execute the `crontab` command, and all other users are allowed to use the `crontab` command.
4. **If both **<mark style="color:orange;">**`cron.allow`**</mark>** and the **<mark style="color:orange;">**`cron.deny`**</mark>** file exists, the **<mark style="color:orange;">**`cron.allow`**</mark>** applies and the **<mark style="color:orange;">**`cron.deny`**</mark>** file is ignored**. As only one of these files should exist, the presence of both files is typically due to a mistake made by the administrator.

To help you undestand how to determine which of these files should be created, consider the following scenario:

**Scenario #3: 100 users, none should be able to use the `crontab` command.**

In this scenario, do not create a `/etc/cron.deny`, but do create an empty `/etc/cron.allow` file to specify that no users have permission to access to `crontab`.

If a user account is not able to use the <mark style="color:red;">`crontab`</mark> command, then the user will receive an error message like the following:

```bash
sysadmin@localhost:~$ crontab -e                                                
You (sysadmin) are not allowed to use this program (crontab)                    
‌⁠​​⁠​ See crontab(1) for more information                                             
sysadmin@localhost:~$ crontab -l                                                
You (sysadmin) are not allowed to use this program (crontab)                    
See crontab(1) for more information
```

**Note that if a user has been denied access to the **<mark style="color:red;">**`crontab`**</mark>** command, this does not prevent any previously created **<mark style="color:orange;">**`crontab`**</mark>** entries from running.** If the user already had <mark style="color:orange;">`crontab`</mark> entries, the administrator needs to remove them, which can be done by using the <mark style="color:red;">**`crontab -u sysadmin -r`**</mark> command.

## `at` Command

The `cron` command is a good tool for scheduling tasks that are required to run at regular intervals. **For scheduling one-time tasks, the **<mark style="color:red;">**`at`**</mark>** and **<mark style="color:red;">**`batch`**</mark>** command are more useful**.

There are 2 prerequisites for running the <mark style="color:red;">`at`</mark> command: the <mark style="color:red;">**`at`**</mark>** package must be installed**, and <mark style="color:red;">**`atd`**</mark>** service must be running**.

To determine if the <mark style="color:red;">`atd`</mark> service is running, use the following command:

```bash
sysadmin@localhost:~$ service atd status                                               
atd  stop/waiting
```

#### Command usage

The <mark style="color:red;">`at`</mark> command executes commands at a specified time or at a time relative to the current time. The command can be used as follows:

```
at TIME
```

| Keyword/Date Format | Significance            |
| ------------------- | ----------------------- |
| `midnight`          | 12:00 a.m. 2nd Mar 2025 |
| `noon`              | 12:00 p.m .1st Mar 2025 |
| `tomorrow`          | 8:00 a.m. 2nd Mar 2025  |
| `next week`         | 8:00 a.m. 8th Mar 2025  |
| `1630`              | 4:30 p.m. 1st Mar 2025  |
| `4:30 PM Mar 20`    | 4:30 p.m. 20th Mar 2025 |
| `now + 2 hours`     | 10:00 a.m .1st Mar 2025 |
| `now + 7 days`      | 8:00 a.m .8th Mar 2025  |

The exact `TIME` specifications can be found in the `at` command man page or the `timespec` help documentary typically found in the `/usr/share/doc/at*` directory.

When the <mark style="color:red;">`at`</mark> command is executed, the user is presented with the `at>` prompt

```bash
sysadmin@localhost:~$ at now
at>
```

Enter the command that requires execution and then press the **Enter** key. Another `at>` prompt will be presented for another command.

When you are finished entering commands to execute, use **Ctrl+d**. The End Of Transmission (`EOT`) message will be displayed, followed by the assigned job number, and the time the job will be executed. **To cancel the **<mark style="color:red;">**`at`**</mark>** job**, press **Ctrl+c** at any time (before scheduling the job with **Ctrl+d**). In the example below, `Planning meeting in 30-minutes` will display in the current terminal window (`/dev/pts/0` - see `who` command output) at 10:00 am.

```bash
sysadmin@localhost:~$ at 1000                                               
warning:  commands will be executed using /bin/sh
at> echo “Planning meeting in 30-minutes” > /dev/pts/0
at>  <EOT>
job 2 at Mon Feb  10  09:26:00  2025
```

{% hint style="warning" %}
The <mark style="color:red;">`at`</mark> and <mark style="color:red;">`batch`</mark> jobs are stored in the `/var/spool/at` directory.
{% endhint %}

#### Additional Examples

The following command will read the command entered at the `at>` prompt and execute it 2 hours from now:

```bash
sysadmin@localhost:~$ at now + 2 hours                                               
warning:  commands will be executed using /bin/sh
at> echo “Marketing meeting in 30-minutes” > /dev/pts/0
at>  <EOT>
job 3 at Mon Feb  10  09:28:00  2025
```

The following command will read the commands from the `backup_script.txt` file at 10:00pm on March 22nd:

```bash
sysadmin@localhost:~$ at -f backup_script.txt  10 pm  Mar 22                                               
warning:  commands will be executed using /bin/sh
job 4 at Mon Feb  10  09:31:00  2025
```

The following command is the same as above, with the addition of the <mark style="color:red;">**`-m`**</mark> option which **sends a mail message to the user after the job is complete**:

```bash
sysadmin@localhost:~$ at -m -f backup_script.txt  10 pm  Mar 22                                               
warning:  commands will be executed using /bin/sh
job 5 at Mon Feb  10  09:33:00  2025
```

### Listing and Deleting `at` Jobs

The _at queue_ <mark style="color:red;">**`atq`**</mark>**  command lists the user's pending jobs**. If the command is being run as the root user, then it will list jobs for all the users

The output of the `atq` command will be similar to the following

```
sysadmin@localhost:~$ atq                                               
2             Mon Feb 10  09:26:00  2025  a   sysadmin 
3             Mon Feb 10  09:28:00  2025  a   sysadmin
4             Mon Feb 10  09:31:00  2025  a   sysadmin
5             Mon Feb 10  09:33:00  2025  a   sysadmin
```

The output includes the following fields:

| Field  | Example      | Significance                                                                         |
| ------ | ------------ | ------------------------------------------------------------------------------------ |
| Number | `2`          | Job number allocated to this job                                                     |
| Date   | `Mon Feb 10` | Date when this job will be executed                                                  |
| Hour   | `09:26:00`   | Hour when this job will be executed                                                  |
| Queue  | `a`          | Name of the queue. Valid queue names are from a-z, with ‘a’ being the default queue. |
| User   | `sysadmin`   | User name of the user who has scheduled this job                                     |

The _at remove_ <mark style="color:red;">**`atrm`**</mark>** command deletes **<mark style="color:red;">**`at`**</mark>** jobs, identified by their job number**. For example to delete a job number 3, use the following command:&#x20;

```bash
sysadmin@localhost:~$ atrm 2    
sysadmin@localhost:~$ atq                                                  
3             Mon Feb 10  09:28:00  2025  a   sysadmin
4             Mon Feb 10  09:31:00  2025  a   sysadmin
5             Mon Feb 10  09:33:00  2025  a   sysadmin
```

{% hint style="danger" %}
To delete a jobm you must either be the owner of the job or the root user
{% endhint %}

### Configuring Access to the `at` Command

While the root user can always use the `at` and `batch` commands, user may not be able to use these commands:

Access to the `at` and `batch` commands is controlled by the following two files:

1. The `/etc/at.allow` file
2. The `/etc/at.deny` file

The format of these files is similar to `cron.allow` and `cron.deny` files. Both the file contain a list of user names, one on each line. The rules mentioned for `cron` access are applicable for `at` access also:

1. If both files do not exist, then all regular users are denied the ability to execute the `at` and `batch` commands.
2. If only the `at.allow` file exists, then only the users listed in the file can execute the `at` and `batch` commands.
3. If only the `at.deny` file exists, then all users listed in this file are denied the ability to execute the `at` and `batch` commands, and all other users are allowed to execute the `at` and `batch` commands.
4. If both the `at.allow` and the `at.deny` files exist, the `at.allow` file applies and the `at.deny` file is ignored. As only one of these files should exist, the presence of both files is typically due to a mistake made by the administrator.

If a user account is not able to use the <mark style="color:red;">`at`</mark> and <mark style="color:red;">`batch`</mark> commands, then the user will receive an error message like the following:

```bash
sysadmin@localhost:~$ at 1000                                                
You do not have permission to use at.
```

Note that if a user has been denied access to the <mark style="color:red;">`at`</mark> and <mark style="color:red;">`batch`</mark> commands, this does not prevent any previously created <mark style="color:red;">`at`</mark> or <mark style="color:red;">`batch`</mark> entries from running.

## `batch` Command

Similar to the <mark style="color:red;">`at`</mark> command, the <mark style="color:red;">**`batch`**</mark>** command is used to schedule one-time tasks**. However, instead of specifying an execution time, <mark style="color:red;">**`batch`**</mark>** commands are executed as soon as the system's load average drops below `0.8`** (80% of CPU usage). The default value of `0.8` **can be changed by using the **<mark style="color:red;">**`-l`**</mark>** option to the **<mark style="color:red;">**`atd`**</mark>** ** command**.**

The <mark style="color:red;">`batch`</mark> command will prompt for command input. A job number is issued upon the successful completion of input. Alternatively, it can read input from a file by using the <mark style="color:red;">`-f`</mark> option. For example, to sort the large `marketing_data` file at the point when the system load average drops below `0.8` execute the following

```bash
sysadmin@localhost:~$ batch
at> sort  ~/marketing_data
at> <EOT>
job 5 at Mon Feb  3  09:26:00  2025
```

## Systemd Timer Units

Many modern system use <mark style="color:red;">`systemd`</mark> rather than the much older system daemon `init` for managing system services. <mark style="color:red;">`Systemd`</mark> is a service and **system manager** that have <mark style="color:red;">`systemd`</mark> as a replacement for the tradition `init` process provide an alternative to <mark style="color:red;">`crond`</mark> for scheduling jobs and managing services, called _systemd timer_

> `systemd` timer jobs are logged via the `systemd` journal, providing output in a centralized place and format.

Timers are essentially files that end in `.timer`, which fall under a category of files called `systemd` _unit files_. **** These unit files contain information about services, sockets, mount points, timers, devices ,and other types of `systemd` units. The <mark style="color:red;">`.timer`</mark>file will **contain configuration information about the task to be executed by the **<mark style="color:red;">**`systemd`**</mark>** timer**. For example:

```
[Unit]
Description=Displays greeting after boot
 
[Timer]
OnBootSec=10sec
Unit=greeting.service
 
[Install]
WantedBy=multi-user.target
```

Below is a breakdown of the file sections:

| Section     | Description                                                                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[Unit]`    | General information about the `systemd` unit file. The `Description` option creates a human-readable name for the `systemd` unit.                                            |
| `[Timer]`   | Contains the options that define when the timer will start and what service to execute.                                                                                      |
| `[Install]` | Contains information about how the unit will be installed. The `WantedBy=` option creates a symbolic link within the target level that “wants” to have that service running. |

<mark style="color:red;">`systemd`</mark> uses two different type of timers: **monotonic** and **realtime**.

*   &#x20;With _**monotonic**_ timers, the `systemd` timer allows a jobs to be executed after an event has occurred. This type can be used to run a job when the system boots (`OnBootSec` option) or a `systemd` unit is active (`OnActiveSec` option)

    ```
    OnBootSec=10sec
    ```


*   With the _**realtime**_ timers work like `cron` and execute a job when a specified time has occurred. To create a realtime timer, the `OnCalendar` option should be used in the `[Timer]` section of the `.timer` file. The format of an `OnCalendar` time entry is:

    ```
    DayofWeek Year-Month-Day Hour:Minute:Second
    ```



The entry below is an example of a timer unit that is executed every day at 9:00 am

```bash
OnCalendar=*-*-* 9:00:00
```

> Similar to the `crontab` format, the asterisk character in the example means any or all values

To specify what days of the week to run a timer, use the `DayofWeek` field. The entry below is an example of a timer unit that is executed every Monday, Wednesday, and Friday at midnight:

```
OnCalendar=Mon,Wed,Fri *-*-* 00:00:00
```

To run a timer on a specifc day of the mont, use the `Day` field. The following entry will run a timer on the first day of every month at 10PM

```
OnCalendar=*-*-01 22:00:00
```

The `OnCalendar` option also uses special expressions such as `hourly`, `daily` ,`monthly` and `yearly` as time values:

```
OnCalendar=daily
```

A `.timer` unit file should correspond to a `systemd` service, which is a `.service` file with the same name. For example, the `greeting.service` unit file created above should have a corresponding service file called `greeting.service`. When the `systemd` timer is activated, the `.service` systemd unit is executed.

## `systemctl` Command

To better manage systemd timers, as well as other services with systemd, some knowledge of the `systemctl` command is required. **The **<mark style="color:red;">**`systemctl`**</mark>** command is used to interface with and control systemd units**. The syntax to use `systemctl` command is the following:

```
systemctl COMMAND UNIT_NAME [OPTION]
systemctl LIST_UNIT
```

To list all the services that are being run on the system, the following command can be used:

```bash
root@localhost:~ systemctl list-units --type=service
UNIT                        LOAD   ACTIVE SUB     DESCRIPTION
accounts-daemon.service     loaded active running Accounts Service
apparmor.service            loaded active exited  AppArmor initialization
blk-availability.service    loaded active exited  Availability of block devices
console-setup.service       loaded active exited  Set console font and keymap
cron.service                loaded active running Regular background program processing daemon
dbus.service                loaded active running D-bus System Message Bus
getty@tty1.service          loaded active running Getty on tty1
```

To show what timers are active on you system, simply run:

```bash
root@localhost:~ systemctl list-timers
NEXT                         LEFT     LAST                         PASSED          UNIT
Sun 2019-10-20 06:49:59 UTC  7h left  Sat  2019-10-19 22:07:15 UTC 1h 16min ago apt-daily-upgrade.timer
Sun 2019-10-20 08:03:49 UTC  8h left  Sat  2019-10-19 22:07:15 UTC 1h 16min ago apt-daily.timer
Sun 2019-10-20 11:18:19 UTC  11h left Sat  2019-10-19 22:32:58 UTC 51min ago motd-news.timer
Sun 2019-10-20 22:22:58 UTC  22h left Sat  2019-10-19 22:22:58 UTC 1h 1min ago systemd-tmpfile-clear.timer
Mon 2019-10-21 00:00:00 UTC  24h left Sat  2019-10-19 22:07:15 UTC 1h 16min ago fstrim.timer
 
5 timers listed.
Pass --all to see loaded but inactive timers, too.
lines 1-9/9 (END)
```

To show all of the systemd timer units on a system, including the inactive systemd timer units, execute the following:

```bash
root@localhost:~ systemctl list-timers --all
NEXT                         LEFT     LAST                         PASSED          UNIT
Sun 2019-10-20 06:49:59 UTC  7h left  Sat  2019-10-19 22:07:15 UTC 1h 16min ago apt-daily-upgrade.timer
Sun 2019-10-20 08:03:49 UTC  8h left  Sat  2019-10-19 22:07:15 UTC 1h 16min ago apt-daily.timer
Sun 2019-10-20 11:18:19 UTC  11h left Sat  2019-10-19 22:32:58 UTC 51min ago motd-news.timer
Sun 2019-10-20 22:22:58 UTC  22h left Sat  2019-10-19 22:22:58 UTC 1h 1min ago systemd-tmpfile-clear.timer
Mon 2019-10-21 00:00:00 UTC  24h left Sat  2019-10-19 22:07:15 UTC 1h 16min ago fstrim.timer
n/a                          n/a    n/a   n/a                     unreadahead-stop.timer
 
6 timers listed.
lines 1-9/9 (END)
```

The systemd timers can be enabled and disabled like any other systemd unit using the following command:

```
systemctl enable name.timer
systemctl disable name.timer
```

Outside of the `.timer` files, the `systemd-run` command can be used to run a _transient_ job, one which does not have a `.timer` file. The `systemd-run` command can be used to run a command or execute a systemd `.service` unit.

For example, to execute the `touch /home/sysadmin/newfile` command one hour after running the `systemd-run` command use:

```bash
root@localhost:~ systemd-run --on-active=”1h” /bin/touch /home/sysadmin/newfile
```

To execute a systemd `.service` unit, use the `--unit` option:

```
root@localhost:~# systemd-run --on-active=”1h” --unit=greeting.service
```

After creating a systemd timer via `systemd-run`, the name of the transient systemd job is returned to you. Executing the `systemctl list-timers` command will display the name of the systemd transient job listed in the `UNIT` column
