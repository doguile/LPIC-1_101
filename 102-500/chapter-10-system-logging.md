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

System loggin is the process of capturing most everything that happens on and to the Linux system and sending the information to log files to be viewed later. Logging system activity is one of the most useful Linux features as it allows an administrator to manage, optimize, secure, and troubleshoot the system and applications.

In the past, the `syslogkd` and `klogd` daemons were two main components of `syslogd` that provided logging facilities for Linux.

{% hint style="info" %}
The `syslogkd` daemon provided applications and programs with logging services while the `klogd` daemon provided logging services for the Linux Kernel.
{% endhint %}

A system logging daemon with additional capabilities called <mark style="color:red;">`syslog-ng`</mark> was later released as a replacement for `syslogd` .The <mark style="color:red;">`syslog-ng`</mark> service provided more detailed message sorting and formating than was available for `syslogd.`

Many Linux distributions have replaced the combination of the `syslogd` and `klogd` daemons with the more recently developed <mark style="color:red;">`rsyslogd`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> daemon. **The **<mark style="color:red;">**`rsyslog`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** service was designed as an alternative to **<mark style="color:red;">**`syslog-ng`**</mark>**.**

{% hint style="info" %}
<mark style="color:red;">`rsyslog`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> uses the basic <mark style="color:red;">`syslog`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> protocol but expands it to provide additional capabilites such as message filtering, queuing to manage offline output, and additional configuration options.
{% endhint %}

The `rsyslog` daemon configuration settings are stored in the `/etc/rsyslog.conf` file. This file contains syntax largely backward compatible with the `/etc/syslog.conf` configuration file for the `syslogd` service running  on legacy Linux systems.

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

![](<../.gitbook/assets/imagen (3).png>)

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

![](<../.gitbook/assets/imagen (2).png>)

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



















































