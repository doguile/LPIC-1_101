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

The system `crontab` allows scheduling of system-wide taks such as log rotations, backups and system database updates. Modifying the system `crontab` for scheduling task requires administrative privileges.

{% hint style="info" %}
Additional system `crontab` jobs that require finer control of their scheduling and do not need environmental variables to be loaded before script execution are stored in the `/etc/cron.d` directory
{% endhint %}

The system <mark style="color:red;">`crontab`</mark> can execute commands either as root or as any other user. For example, if a user does not have access to <mark style="color:red;">`cron`</mark>, but needs to have a particular script run at the end of the day on a daily basis, then they can ask the administrator to add an entry in the system <mark style="color:red;">`crontab`</mark>.

The following is an example of a typical system `crontab` file:

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

The `crontab` entries can be used to schedule tasks that can occur as frequently as once every minute to as infrequently as once a year. The format of a `crontab` entry is:

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

The `PATH` variable is very important in the event that relative pathnames are used when specifying commnads.

It is important to note that the <mark style="color:red;">`crond`</mark> utility does not read user initializations files when it executes commands. This means that creating variables like the `PATH` variable is very important

The `MAILTO` variable is important for any command that produces output when executed. There is no assumption that the user is logged in when the command runs, so the output of the command is sent to the user's email address by default.

### Crontab Keywords

As defined in the `crontab` man page, the following special keywords can also be used to specify a time in place of the normal five fields representing time values:

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





























