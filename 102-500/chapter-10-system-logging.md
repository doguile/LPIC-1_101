---
description: '108.2: System logging v2  Weight: 4'
---

# Chapter 10: System Logging

<details>

<summary>Key terms</summary>

`/etc/logrotate.conf` Contains directives for the default configuration of the logrotate utility

`/etc/logrotate.d/` Service-specific settings for the logrotate tool are controlled with configuration files in the /etc/logrotate.conf

`/etc/rsyslog.conf` The rsyslog daemon configuration settings are stored in the /etc/rsyslogd.conf file

`/var/log/journal` A binary database where log information is written to on systems using systemd-journal.

`journactl` The journalctl command is used to interact with the systemd-journald

`logger` Command used to send messages to the system logging facility. One of the main uses of the logger command is to verify that entries that have been made in the syslog.conf file are working as expected.

`logrotate` The logrotate tool is used to allow a system administrator to automate the rotation of log files with different settings for different services.

`systemd-cat` The systemd-cat command allows you to add to the systemd-journal data

</details>

## Introduction

System **logging is the process of capturing most everything that happens** on and to the Linux system and sending the information to log files to be viewed later. Logging system activity is one of the most useful Linux features as it allows an administrator to **manage**, **optimize**, **secure**, and **troubleshoot** the system and applications.

In the past, the <mark style="color:red;">`syslogkd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and <mark style="color:red;">`klogd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemons were two main components of <mark style="color:red;">`syslogd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> that provided logging facilities for Linux.

{% hint style="info" %}
The <mark style="color:red;">`syslogkd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon provided applications and programs with logging services while the <mark style="color:red;">`klogd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon provided logging services for the Linux Kernel.
{% endhint %}

**A system logging daemon with additional capabilities called **<mark style="color:red;">**`syslog-ng`**</mark>** was later released as a replacement for **<mark style="color:red;">**`syslogd`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> .The <mark style="color:red;">`syslog-ng`</mark> service provided more detailed message sorting and formating than was available for `syslogd.`

Many Linux distributions have replaced the combination of the `syslogd` and `klogd` daemons with the more recently developed <mark style="color:red;">`rsyslogd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon. **The **<mark style="color:red;">**`rsyslog`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** service was designed as an alternative to **<mark style="color:red;">**`syslog-ng`**</mark>**.**

{% hint style="info" %}
<mark style="color:red;">`rsyslog`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> uses the basic <mark style="color:red;">`syslog`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> protocol but expands it to provide additional capabilites such as **message filtering**, **queuing** to **manage offline output**, and additional configuration options.
{% endhint %}

The <mark style="color:red;">`rsyslog`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon configuration settings are stored in the <mark style="color:orange;">`/etc/rsyslog.conf`</mark> file. This file contains syntax largely backward compatible with the `/etc/syslog.conf` configuration file for the `syslogd` service running  on legacy Linux systems.

## Log File Location

Most of the message processing tasks are handled by the <mark style="color:red;">`rsyslogd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon. This daemon is configured by the settings in the `/etc/rsyslog.conf` file. By default, **most of the log files are stored in the `/var/log` directory.** For services or programs that maintain their own logging system instead of sending log messages to the `rsyslogd` daemon, these services typically store their log messages in a plain ASCII file that is in a subdirectory of the `/var/log` directory, such as the `/var/log/httpd` directory for the `httpd` daemon.

Some standard log files that are usually found in the `/var/log` directory are listed below.

| File                                      | Purpose                                        |
| ----------------------------------------- | ---------------------------------------------- |
| `/var/log/messages` or `/var/log/rsyslog` | General message and system-related information |
| `/var/log/secure` or `/var/log/auth.log`  | Authentication log                             |
| `/var/log/maillog`                        | Mail server logs                               |
| `/var/log/kern.log`                       | Kernel logs                                    |
| `/var/log/boot.log`                       | System boot log                                |
| `/var/log/cron.log`                       | `crond` logs                                   |

## `rsyslogd` Configuration

The man page for `rsyslog.conf` provides some good examples of how to **configure what will be logged and where it will be written**.

{% hint style="info" %}
For describing what will be logged, the configuration file uses a _selector._ The selector is made up of two parts; a _facility_ and a _priority_, separated by a period `.` character.&#x20;

An _action_ is used to describe where to send the log information.

Each line of the configuration file will be specify both a selector and an action.
{% endhint %}

In the example two-line entry below, `authpriv.*` is the selector, `/var/log/secure` is the action, and the line beggining with `#Log` is a comment line explaining the selector/action line:

```
authpriv.*                             /var/log/secure
# Log authentication messages in the /var/log/secure file
```

#### Facility

![](<../.gitbook/assets/imagen (1).png>)

**The facility identifies the part of the system that produced some kind of message**. For example, messages from the Linux kernel can be selected using the `kern` facility.

To make up the first part of a selector, the following standard facilities are identified be these keywords:

| Facility             | Description                                 |
| -------------------- | ------------------------------------------- |
| `auth`               | Security and authorization-related commands |
| `authpriv`           | Private authorization messages              |
| `cron`               | The `cron` daemon                           |
| `daemon`             | System daemons                              |
| `ftp`                | The `ftp` daemon                            |
| `kern`               | The kernel                                  |
| `lpr`                | The BSD printer spooling system             |
| `mail`               | `sendmail` and other mail-related software  |
| `mark`               | Timestamps generated at regular intervals   |
| `news`               | The Usenet news system                      |
| `security`           | Same as `auth`                              |
| `rsyslog`            | `rsyslogd` internal messages                |
| `user`               | User processes                              |
| `uucp`               | Reserved for UUCP                           |
| `local0` to `local7` | Eight flavors of local message              |

**Priority**

![](<../.gitbook/assets/imagen (4).png>)

The other part of the selector is the _priority_, which defines the severity of the message.&#x20;

Priority is ordered from lowest to highest in this order.

| Priority             | Description                           |
| -------------------- | ------------------------------------- |
| `debug`              | For debugging only                    |
| `info`               | Informational messages                |
| `notice`             | Things that might merit investigation |
| `warning (or warn)`  | Warning messages                      |
| `err`                | Other error conditions                |
| `crit`               | Critical conditions                   |
| `alert`              | Urgent situations                     |
| `emerg` (or `panic`) | Panic situations                      |

Priorities that are specified mean not only the level specified but anything of higher priority, as well.

There is also a special priority called `none` ,which means do not log from that facility. Typically `none` is used in conjunction with wildcard settings to limit the scope of the wildcard.

#### Selector

![](<../.gitbook/assets/imagen (3) (1).png>)

The selector is comprised of both the _facility_ and the _priority_ separated by a period `.` character. The following table illustrates some common selectors. Note that an asterisk `*` wildcard character can be used to represent either all facilities or all priorities in a selector:

| Selector                    | Description                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------- |
| `*.*`                       | All facilities and priorities                                                                 |
| `*.info`                    | All facilities at `info` priority or higher                                                   |
| `kern.*`                    | Select all kernel messages                                                                    |
| `mail.warning`              | Messages from the `mail` facility at a warning priority or higher                             |
| `cron,lpr.err`              | Messages from the `cron` or `lpr` facility at an `err` priority or higher                     |
| `cron.err;cron.!alert`      | Messages from the `cron` facility at an `err` priority or higher, but not at `alert` priority |
| `mail.=err`                 | Only `err` messages from the mail facility                                                    |
| `*.info;mail.none;lpr.none` | Select messages from all facilities except `mail` and `lpr`                                   |

#### Action

![](<../.gitbook/assets/imagen (2) (1) (1).png>)

Combining a selector with an action results in a complete line in the `/etc/rsyslog.conf` file. The most common action is to specify the absolute path, the file that will store the information that is selected. The following table demonstrates the available actions:

| Action                  | Description                                                                                                                                                                               |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/path/to/file`         | Specify the full absolute path for the log file                                                                                                                                           |
| `-/path/to/file`        | The `-` before the path means to not sync after writing each log message (better for system performance for log files that are often written to, such as mail log files on a mail server) |
| `\|/path/to/named/pipe` | Specify a pipe symbol and a path to a named pipe file created with `mkfifo` (make first-in, first-out)                                                                                    |
| `/dev/tty10`            | Specify a terminal or console, such as `/dev/console`                                                                                                                                     |
| `@10.0.0.1`             | Specify an `@` symbol with the IP address or resolvable hostname or a remote host                                                                                                         |
| `student,maya,joe`      | Specify a list of users whose terminals will have the message displayed if the users are currently logged into the system                                                                 |
| `*`                     | Send to the terminal of everyone who is logged in                                                                                                                                         |

Below are some sample lines from a `rsyslog.conf` file. Each line starting with a hash `#` symbol is a comment that describes the line above it.

```bash
*.info;mail.none;authpriv.none;cron.none    /var/log/messages
# Log everything but mail, authentication and cron in 
# the /var/log/messages file
 
authpriv.*                             /var/log/secure
# Log authentication messages in the /var/log/secure file
 
mail.*                                 -/var/log/maillog
# Log mail messages in the /var/log/maillog file, not 
# syncing each time
 
cron.*                                    /var/log/cron
# Log cron messages in the /var/log/cron file
 
local7.*                                   /var/log/boot.log
# Log local7 messages to the /var/log/boot.log file
 
*.emerg                            *
# Send emergency messages to the terminal of all logged in users
```

**Keeping track of all the activities that take place in any system is an important part of maintaining a secure system**. If careful observation is made on what usually happens through different events, such as understanding when users log into the system, then log files can be used for spotting unusual activities.

By default, **the **<mark style="color:red;">**`rsyslogd`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon only accepts the logs which are generated by the localhost**, and it will create a `MARK` message in the file every 20 minutes.

If an administrator wants to enable a host to be a central log server, they can edit the `/etc/rsyslogd.conf` file.

To set up the host to do remote logging on a central server an entry needs to be added to the `rsyslogd.conf` file. For example, to send `warning` or higher priority messages to the log server (192.168.0.254 in this example), the `rsyslogd.conf` entry would appear as follows:

```
*.warning		@192.168.0.254:514
```

After setting the `rsyslogd` options, the logging service would need to be restarted or the system rebooted for the change to take effect.

{% hint style="info" %}
Another way to verify that the logging service is running would be to check the status of the `rsyslog` service by executing:

```
rsyslogd -N1
```
{% endhint %}

## `logger` Command

The <mark style="color:red;">`logger`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is **used to send messages to the system logging facility**. The following options can be used with <mark style="color:red;">`logger`</mark>

| Option        | Purpose                                              |
| ------------- | ---------------------------------------------------- |
| `-i`          | Log the process id of the logger process             |
| `-s`          | Log the message to standard error and the system log |
| `-f file`     | Use the message found in the specified file          |
| `-p selector` | Send the message as the selector like `mail.info`    |
| `-t tag`      | Mark the message line in the log with a tag          |

One of the main uses of the <mark style="color:red;">`logger`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is to **verify that the entries that have been made in the **<mark style="color:orange;">**`rsyslog.conf`**</mark>** file are working as expected**. For example, consider the following entry which is designed to isolate `mail` facility errors into a file named `/var/log/mail.err`&#x20;

```
mail.=err    /var/log/mail.err
```

After restarting the `rsyslogd` ,this entry could be tested by using the following `logger` command:

```bash
sysadmin@localhost:~$ logger -t TEST -p mail.err 'Testing mail.err entry'
```

Viewing the `rsyslog` contents of the `/var/log/mail.err`  file would display something like the following:

```bash
sysadmin@localhost:~$ tail -5 /var/log/mail.err  
...
Sep 26 16:57:15 localhost TEST: Testing mail.err entry
```

## rsyslog Log Management

Most of the information loggend on a Linux system is useful for a limited amount of time. Since log files growe over time, an administrator should institute logging policies that determine what to do with the log files and how often to take action.

Whatever policies are implemented, the maintenance of log files should be automated with `cron`&#x20;

It is recommended that all administrators become familiar with log review and management best practices in order to maintain an optimized and secure system.

### `logrotate`

The <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> tool is used to allow a system administrator to **automate the rotation of log files** with different settings for different services.

General settings for <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> are controlled by the `/etc/logrotate.conf` file and s**ervice-specific settings are controlled with configuration files** in the `/etc/logrotate.d/` directory. Below is an example of a default `/etc/logrotate.conf` file.

```bash
# see "man logrotate" for details 
# rotate log files weekly       
weekly             
  
# use the syslog group by default, since this is the owning group      
# of /var/log/syslog. 
su root syslog 
 
# keep 4 weeks worth of backlogs   
 rotate 4 
 
# create new (empty) log files after rotating old ones 
create 
 
# uncomment this if you want your log files compressed 
#compress 
 
# packages drop log rotation information into this directory 
include /etc/logrotate.d 
 
# no packages own wtmp, or btmp -- we'll rotate them here 
/var/log/wtmp {  
      missingok 
      monthly 
      create 0664 root utmp 
      rotate 1
} 
 
/var/log/btmp { 
      missingok
monthly 
      create 0660 root utmp  
      rotate 1
} 
 
# system-specific logs may be configured here
```

The `/etc/logrotate.conf` file contains directives for the default configuration of the <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility. The following table summarizes the settings found in the `/etc/logrotate.conf` configuration file above

| Directive                     | Purpose                                                                                                                                                                          |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `weekly/daily/monthly/yearly` | Rotates the logs at the specified time interval                                                                                                                                  |
| `rotate 4`                    | Determines how many rotated logs are kept before <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> deletes older logs |
| `compress`                    | Specifies <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> to compress rotated logs                                  |
| `missingok`                   | Tells <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> not to return an error if the log file is not found           |

Files in the `/etc/logrotate.d` directory are loaded by the include `/etc/logrotate.d` statement in the `/etc/logrotate.conf` file.

```bash
# packages drop log rotation information into this directory 
include /etc/logrotate.d
```

**These files allow the system administrator to have different configurations for the logs of different services.** The `/etc/logrotate.conf` directives that are not part of a stanza after a specified log file are used as the defaults for files managed by the <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility. If a specific setting is set for the log file in `/etc/logrotate.conf` or a configuration file in `/etc/logrotate.d`/, it will override the defaults.

**The primary use of having separated configurations is to organize **<mark style="color:red;">**`logrotate`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** configurations**. For example, if an application has multiple log files, all <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> configuration for the logs can be done in one configuration file: `/etc/logrotate.d/`- The same directives can be used in the different <mark style="color:red;">`logrotate`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> configuration files.

For example, maybe you have a service that needs longer retention than any of the other log files. In that case, you can create a `logrotate` configuration file in `/etc/logrotate.d/` that has the needed `logrotate` directives for the service's logs. Below is an example of a `logrotate` configuration file that has settings different than the settings in `/etc/logrotate.conf`&#x20;

```bash
/var/log/apt/term.log {  
      rotate 12
      monthly
      compress
      missingok
}
/var/log/history.log {
      rotate 12
      monthly 
      compress
      missingok
}
```

Note that the full path to the log file needs to be specified and then a curly `{` brace to open the stanza. The desired directives need to be entered into the stanza, with only one directive per line. The stanza then should be closed with another curly `{` brace.

In the example `/etc/logrotate.d/` file above, the `/var/log/apt/term.log` and `/var/log/history.log` lines specify the location of the log files that the following stanza pertains to. The `rotate` directive for each log file tells the `logrotate` tool how many rotated files to keep before it deletes an old log file. The `monthly` directive tells `logrotate` how often the log file needs to be rotated. The rotated log files will be compressed due to the `compress` directive. Lastly, the `missingok` directive prevents an error from appearing if `logrotate` doesn't find the log file when it attempts to rotate the log file.

## systemd journal

On systems using `systemd` as their `init` system, <mark style="color:red;">**`rsyslog`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** has been replaced by the **<mark style="color:red;">**`systemd-journal`**</mark> .On systems using `rsyslog` and older logging systems, log files are not kept in a central location. Each appplication could have files in different places, which makes it a challenge to find useful log information, especially in urgent situations such as a service unexpectedly not running.

A `systemd` _unit_ named <mark style="color:red;">`systemd-journal`</mark> handles logs from other `systemd` units. Using the <mark style="color:red;">`systemd-journal`</mark>, the log information is written to binary databases in the <mark style="color:orange;">**`/var/log/journal/`**</mark> directory instead of writing to text files like `rsyslog`&#x20;

While the log information is available in whichever format is being used, the tools to access and manipulate the log information differs depending on the format the logs are stored in. For text-based logs from `rsyslog` ,Linux tools such as `cat`,`less`, `grep`,`head`, and `tail` can directly view the log files. With the `systemd-journal` you need to use another program (usually <mark style="color:red;">`journalctl`</mark>) to interpret and present the logs in a huma-readable format.

## systemd journal Configuration

The `/etc/systemd/journald.conf` file controls the `systemd-journal`, but the most used directive controls the amount of space that is used for storing _**persistent**_** logs found in **<mark style="color:orange;">**`/var/log/journal/`**</mark> if the directory exists. Otherwise, the <mark style="color:red;">**`systemd-journal`**</mark>** store logs in volatile memory (RAM)** located at <mark style="color:red;">`/run/log/journal`</mark> .&#x20;

* Persistent storage is a type of storage used to **ensure that data is not modified after it is stored** and is available even if updates are made to the storage software.
* Files stored in volatile memory **disappear when a computer is reset**.&#x20;

By default, the `systemd-journal` uses up to 10% of the total partition space for persistent journal storage, with a cap of 4GB. However, that setting can be controlled with the `SystemMaxUse` directive in `/etc/systemd/journald.conf`&#x20;

The following table summarizes common directives used in the `/etc/systemd/journald.conf` configuration file:

| Directive           | Purpose                                                                                                                                                                                                                                                                                                                                                 |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Storage`           | Determines how the journal will be stored. The `volatile` option keeps the journal only in memory. The `persistent` option stores the log data on the disk. The `auto` option stores to the disk also, but will not create a log file it doesn’t already exist. The `none` option does not store the journal data, but only displays it on the console. |
| `Compress`          | Specifies if the journal logs should be compressed or not.                                                                                                                                                                                                                                                                                              |
| `SystemMaxUse`      | Limits the amount of space a log can use on the disk. By default, the limit is set to 10% of the total disk space with a cap of 4GB.                                                                                                                                                                                                                    |
| `SystemMaxFileSize` | Specifies the maximum size that an individual journal file can be before the file is rotated.                                                                                                                                                                                                                                                           |

## systemd journal Log Management

To interact with the `systemd-journald`, the <mark style="color:red;">`journalctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is used. The output from the <mark style="color:red;">`journalctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command uses a pager by default.

In addtion, the output from `journalctl` can span months and provide more information than is needed; therefore, command flags can be used to help narrow down the output. The table below provides example flags for the `journalctl` command.

| Option              | Purpose                                                                                                              |
| ------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `-b`                | Limits output to only journal data since the **last time the system booted**.                                        |
| `-u <systemd unit>` | Limits output to only contain output from the specified `systemd` unit. An example would be “journalctl -u postfix”. |
| `-n <number>`       | Shows only the last of lines specified.                                                                              |
| `-r`                | **Reverses chronology**. Shows logs with the newest first and then each older entry in order.                        |

The power of <mark style="color:red;">`journalctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> comes from the ability to use flags at the same time to output the data needed from the logs. For example, the command below will show only entries since the last boot (<mark style="color:red;">`-b`</mark>), in reverse chronological order (<mark style="color:red;">`-r`</mark>), and only for the systemd-timedated `systemd` unit (<mark style="color:red;">`-u systemd-timedated`</mark>)

```bash
sysadmin@localhost:~$ journalctl -b -r -u systemd-timedated
-- Logs begin at Thu 2019-10-31 23:40:51 CDT, end at Fri 2019-11-01 11:13:54 CDT. --
Nov 01 11:07:17 ubuntu systemd-timedated[661]: Changed timezone to ‘CST6CDT’. 
Nov 01 11:06:45 ubuntu systemd[1]: Started Time & Date Service.
Nov 01 11:06:45 ubuntu systemd[1]: Starting Time & Date Service...
```

Since the more recent entries in the logs are found at the end of the log file, in many cases, it makes sense to limit output show only the last lines of the log file. By default, the <mark style="color:red;">`-n`</mark> flag used with the <mark style="color:red;">`journalctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command will display the last ten most recent log entries.

```bash
sysadmin@localhost:~$ journalctl -n 
-- Logs begin at Mon 2019-06-17 16:37:10 UTC, end at Wed 209-11-06 04:58:32 UTC. --
Nov 06 04:58:32 ubuntu systemd[1]: Starting User Manager for UID 0…
Nov 06 04:58:32 ubuntu systemd[611]: pam_unix(systems-user:session): session opened for user root by
Nov 06 04:58:32 ubuntu systemd[611]: Reached target Timers.
Nov 06 04:58:32 ubuntu systemd[611]: Reached target Paths.
Nov 06 04:58:32 ubuntu systemd[611]: Reached target Sockets.
Nov 06 04:58:32 ubuntu systemd[611]: Reached target Basic System.
Nov 06 04:58:32 ubuntu systemd[611]: Reached target Default.
Nov 06 04:58:32 ubuntu systemd[1]: Startup User Manager for UID 0.
Nov 06 04:58:32 ubuntu login[616]: ROOT LOGIN on `/dev/tty1'
```

To **manage the log files** created by `systemd-journald`,the <mark style="color:red;">`journalctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command **has flags to clear the log and set rotation** due to time or size limits. These flags are summarized in the table below:

| Option                 | Purpose                                                                                                                                         |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `--rotate`             | Rotates all of the `systemd-journald` log files immediately.                                                                                    |
| `--vacuum-time=<time>` | Removes any `systemd-journald` log data older than the time specified. Time can be in minutes (m), hours (h), weeks (weeks), or months (month). |
| `--vacuum-size=<size>` | Removes the oldest `systemd-journald` log data until the log data takes less than the size listed.                                              |

To demonstrate, the following command clears all of the `systemd-journald` log data that is older than 2 weeks:

```bash
sysadmin@localhost:~$ journalctl --vacuum-time=2weeks
```

{% hint style="info" %}
Please note that no output will be generated; however, using the `journalctl` command again to view the logs will only display `systemd-journald` log data that is 2 weeks old or newer.
{% endhint %}

In some cases, a system will fail while booting, which makes it difficult to access the `systemd-journald` logs. The `systemd-journald` logs may contain information as to why the system failed to boot, so it is important to be able to access these logs. In these cases, the failed system's disk can be mounted on a recovery system.

After mounting the disk, the <mark style="color:red;">**`systemd-nspawn`**</mark> command can be **used to view the `systemd-journald` log of the failed system.**

{% hint style="warning" %}
First, boot the failed system via a rescue ISO image. Then create a directory in the `/mnt` directory to mount the failed system's disk to. Netx, mount the failed system's disk.

```bash
sysadmin@localhost:~$ mkdir /mnt/failedsys
sysadmin@localhost:~$ mount /dev/vda1 /mnt/failedsys
```

If the disk device name for the failed system is unknow, using the `lsblk` command will show you all disks the system detects.

After mounting the failed system's disk, launch a `systemd-nspawn` container that allows access to the `systemd-journald`&#x20;

```bash
sysadmin@localhost:~$ systemd-nspawn --directory /mnt/failedsys --boot -- --unit rescue.target
Spawning container failedsys on /mnt/failedsys.
Press ^] three times within 1s to kill container
```

Once the <mark style="color:red;">`systemd-nspawn`</mark> container has been spwaned, the normal <mark style="color:red;">`journalctl`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> commands can be used to view the failed system's `systemd-journald` logs. Since the system was booted on a rescue ISO, the system can then just be powered off once you have viewed the `systemd-journald` logs.
{% endhint %}

### systemd-cat

Since `systemd-journald` stores data in a binary database, instead of text files, adding data to the logs requires the use of a tool. The `systemd-cat` command allows you to add to the `systemd-journald` data.

> The output from a command can be piped into `systemd-cat` to have the outputfrom the command entered into the logs.

Similar to the <mark style="color:red;">`logger`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, using <mark style="color:red;">`systemd-cat`</mark> to send command output to logs can be used to verify that entries that have been made in the `/etc/systemd/journald.conf` file are working as expected. The `systemd-cat` command can be executed with the following syntax:

```
systemd-cat [OPTIONS...] [COMMAND] [ARGUMENTS...]
```

When piping a command to <mark style="color:red;">`systemd-cat`</mark> ,all of the output is added to the `systemd-journald` ,and no output shows up on the screen. **To allow the output to show on the screen and add the output to the logs, you can use the **<mark style="color:red;">**`tee`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command.** Below is an example of adding the process list for the user to the `systemd-journald` while showing the output to the terminal.

```bash
sysadmin@localhost:~$ ps | tee /dev/tty1 | systemd-cat
  PID TTY          TIME CMD
  586 tty1     00:00:00 bash
  668 tty1     00:00:00 ps
  669 tty1     00:00:00 tee
  670 tty1     00:00:00 cat
sysadmin@localhost:~$ journalctl -r
-- Logs begin at Fri 2019-11-01 09:44:11 CDT, end at Fri 2019-11-01 20:56:04 CDT. --
Nov 01 20:56:02 ubuntu cat[670]: 670 pts/0    00:00:00 cat
Nov 01 20:56:02 ubuntu cat[670]: 669 pts/0    00:00:00 tee
Nov 01 20:56:02 ubuntu cat[670]: 668 pts/0    00:00:00 ps
Nov 01 20:56:02 ubuntu cat[670]: 586 pts/0    00:00:00 bash
Nov 01 20:56:02 ubuntu cat[4567]: PID TTY          TIME CMD
```

By examining the output of the `journalctl -r` command above, we can see the process list appears in the logs.

{% hint style="info" %}
Consider this

If journaling is disabled on a production server, it could indicate that the system has been compromised. The following output can verify whether journaling has been disabled.

```
sysadmin@localhost:~$ echo "Hello" | systemd-cat
Failed to create stream fd: No such file or directory
```

This can also be confirmed with the following command:

```
sysadmin@localhost:~$ sudo systemctl list-unit-files | grep journal
```
{% endhint %}
