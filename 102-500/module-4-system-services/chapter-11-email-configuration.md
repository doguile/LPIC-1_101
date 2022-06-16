---
description: '108.3: Mail Transfer Agent (MTA) basics v2  Weight: 3'
---

# Chapter 11: Email Configuration

<details>

<summary>Key terms</summary>

<mark style="color:red;">`exim`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Developed in 1995 as another **alternative to the `sendmail` program**.

<mark style="color:red;">`mail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> This commnad is built-in-text-based MUA for Linux that **does not support attachments**. The mail command interface **used to send, read, reply and delete mail**. `mail` is handy for administrators to send/receive mail when a **graphical interface is not running** of for simple **email testing**.

<mark style="color:red;">`mailq`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Command used to **query the mail messages queued for delivery**

<mark style="color:red;">`newaliases`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Command used to **create and update the forwarding database** (`/etc/mail/aliases.db`) accessed by sendmail using the contents of the `/etc/mail/aliases` flat data file

<mark style="color:red;">`postfix`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Alternative to `sendmail`. The main advantages of the postfix program is its **enhanced security and ease of administration**

<mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> Is a general purpose **internetwork email routing** facility that supports many kinds of mail-transfer and delivery methods, including the SMTP used for email transport over the internet. `sendmail` has two major components: the sendmail program and the sendmail configuration file to allow for complex customization.

<mark style="color:red;">`sendmail emulation layer commands`</mark> A set of commands the administrator uses that **emulate `sendmail` in order to test the sendmail daemon** is functioning properly

<mark style="color:red;">`~/.forward`</mark> When placed in a user's home directory, `.forward` is used for automatically forward mail to the addresses or aliases specified in this file.

</details>

## Introduction

The sending and receiving of email has been an important form of communication since before the advent of the internet. **The program or email client used to retrieve, read, and compose email is known as a **_**Mail User Agent (MUA)**_. Linux comes with several MUAs, such as the <mark style="color:red;">`mail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> program, for example, but organizations can opt to use other 3rd party graphical MUAs (Thunderbird, etc) or text-based MUAs such as Mutt and Elmo.

**The transfer of messages between machines and routing them to the correct destination is done by **_**Mail Transfer Agents (MTAs)**_. In this module, the basics of email and four common mail transfer agents (MTAs), <mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> ,<mark style="color:red;">`postfix`</mark>, <mark style="color:red;">`exim`</mark>, and <mark style="color:red;">`qmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> will be explored.

The core functionality of each MTA is identical, but the format of configuration files is distinct.

![](<../../.gitbook/assets/imagen (2).png>)

## `mail` Command

The <mark style="color:red;">**`mail`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> command is a built-in-text-based _**Mail Userg Agent (MUA)**_ for Linux that **does not support attachments.** The <mark style="color:red;">`mail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command provides a command interface for the mail system.&#x20;

> All the basic end-user operations such as sending, reading, replying, and deleting mail can be performed using the various command options that the `mail` command provides.

<mark style="color:red;">`mail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> is handy for administrators to **send/receive mail when a graphical interface is not running of for simple email testing.**

```bash
sysadmin@localhost:~$ mail
"/var/mail/sysadmin": 3 messages 3 new                                          
>N   1 System Administrat Fri Jan  9 05:54  19/817   Your Email with Attachmen  
 N   2 System Administrat Fri Jan  9 05:56  19/783   Your First Email Message   
 N   3 System Administrat Fri Jan  9 05:59  20/895   You Could Be a Millionair  
?
```

If you don't have any unread mail messages, you will receive a message like the following:

```bash
sysadmin@localhost:~$ mail                                                      
No mail for sysadmin
```

To view the list of all commands that are available while in the `mail` utiltity, type `list` after the mail prompt

```
? list                                                                          
alias alternates Copy cd chdir copy decode delete discard dp dt echo edit else  
endif envelope exit Followup file folder folders followup from group headers    
help hold if ignore incorporate list mail mbox next nos nounfold Print pipe     
preserve previous print quit Reply Respond reply respond retain Save save       
sendheader set setq sen setenv shell size source struct summary Type tag top    
touch type unalias undelete unfold unset untag variable version visual          
warranty Write write xit z ? ! = # * + | -                                      
?
```

The following is a summary of basic mail commands:

| Mail Command       | Used to                                                  |
| ------------------ | -------------------------------------------------------- |
| `n`                | Read the next message (same as pressing **Enter**)       |
| `h`                | Display header information for all messages              |
| `q`                | Quit mail and preserve unread messages                   |
| `x`                | Exit mail and preserve all messages (even if deleted)    |
| `r [message #]`    | Reply to current (indicated by >) or specified message # |
| `p`                | Print the message again (re-read)                        |
| `d [message list]` | Delete current (indicated by >) or specified message(s)  |

To view the message contents, type the message number (from the 2nd column) after the mail prompt.

```bash
sysadmin@localhost:~$ mail
"/var/mail/sysadmin": 3 messages 3 new                                          
>N   1 System Administrat Fri Jan  9 05:54  19/817   Your Email with Attachmen  
 N   2 System Administrat Fri Jan  9 05:56  19/783   Your First Email Message   
 N   3 System Administrat Fri Jan  9 05:59  20/895   You Could Be a Millionair  

? 2 
Date: Fri, 9 Jan 2015 05:56:25 GMT                                              
From: System Administrator <sysadmin@c92489786c0b.localdomain>                  
Subject: Your First Email Message                                               
To: <sysadmin@c92489786c0b.localdomain>                                         
 
 
Hello,                                                                          
  
We hope you are enjoying your very first email message.                         
 
Sysadmin User   
                                                                
?
```

To **redisplay the header information for all messages in the inbox,** type <mark style="color:red;">`h`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> after the mail prompt. Note message `2` now indicates `R` instead of new `N` and the current message prompt `>` is next to it:

```bash
? h
  N   1 System Administrat Fri Jan  9 05:54  19/817   Your Email with Attachmen  
 >R   2 System Administrat Fri Jan  9 05:56  19/783   Your First Email Message   
  N   3 System Administrat Fri Jan  9 05:59  20/895   You Could Be a Millionair  
?
```

To **delete the current message (indicated by `>` ), type **<mark style="color:red;">**`d`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** or **<mark style="color:red;">**`delete`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> after the mail prompt. Redisplay the inbox using `h` to verify the message is deleted and note the current message indicator is now next to message `3`&#x20;

```bash
 N   1 System Administrat Fri Jan  9 05:54  19/817   Your Email with Attachmen  
 >R   2 System Administrat Fri Jan  9 05:56  19/783   Your First Email Message   
  N   3 System Administrat Fri Jan  9 05:59  20/895   You Could Be a Millionair  

? d

? h
  N   1 System Administrat Fri Jan  9 05:54  19/817   Your Email with Attachmen  
 >N   3 System Administrat Fri Jan  9 05:59  20/895   You Could Be a Millionair  
?
```

To **reply to the current message**, type <mark style="color:red;">`r`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> or <mark style="color:red;">`reply`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> after the mail prompt.

This will display the message with the `To` and `Subject` fields filled as per the original message and place the cursor in the message body where you type your reply. To send the reply, press **Enter** to place the cursor on a blank line, then type **Ctrl  + D**. `EOT` (End of Transmission) will be displayed after the message has been successfully sent. The example below shows a reply after reading message `3`

```bash
 N   1 System Administrat Fri Jan  9 05:54  19/817   Your Email with Attachmen  
 >N   3 System Administrat Fri Jan  9 05:59  20/895   You Could Be a Millionair  

? 3                                                                             
Date: Fri, 9 Jan 2015 05:59:15 GMT                                              
From: System Administrator <sysadmin@c92489786c0b.localdomain>                  
Subject: You Could Be a Millionaire!                                            
To: <sysadmin@c92489786c0b.localdomain>                                         
‌⁠​​⁠​ 
From: Bill Gates <billgates@microsoft.com>

This is your lucky day. Be sure to pass this on to eight friends if you want a chance to be eligible for millions of dollars!                                    
 
Your Friend,                                                                    
Bill 
 
? r

To: sysadmin@c92489786c0b.localdomain,sysadmin@c92489786c0b.localdomain         
Subject: Re: You Could Be a Millionaire!
 
Thanks Bill! 
 
-Dan
EOT
```

{% hint style="info" %}
To delete/reply a specific message, use `d/r` followed by a message number, such as `d2` or `r1`, followed by **Enter**
{% endhint %}

To **end the current mail session and preserver any unread messages**, type <mark style="color:red;">`q`</mark> or `quit` after the mail prompt.

```
? q
sysadmin@localhost:~$
```

### Startup Options

Some of the key options of the <mark style="color:red;">`mail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command are:

| Option                                         | Meaning                                                                                                                                        |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">`-f file_name`</mark> | <p>Read and process the contents of the mailbox or the specified file</p><p>Upon quitting, undeleted messages are written back to the file</p> |
| <mark style="color:red;">`-n`</mark>           | Do not read `/etc/mail.rc` at startup time                                                                                                     |

### Sending Mail

**To send new mail to a user, type **<mark style="color:red;">**`mail`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** on the command line,** followed by the recipient's email address. The program will request a `subject:`, which can be left blank by pressing the **Enter** key.

After providing the `Subject:`, the cursor is placed on a blank line to type the body of your email. Once the message is completed, type **Ctrl + D** on a blank line. See the following example:

```bash
sysadmin@localhost:~$ mail root                                                 
Cc:                                                                             
Subject: Unable to access files on server                                       
Hi,                                                                             
  
I can't access the files on the data server.  Please help.                      
 
-Joe
EOT
```

### `mailq` Command

Normally, messages are stored in a mail queue ("post office") where they are held for a short period of time until emails that arrived first are sent. This is known as the **FIFO** method delivery. Once the message has been sent to a remote mail server, it is removed from the mail queue.

If a message is in the mail queue for more than a few minutes, there is a likely a problem with the delivery. This could be a temporary problem, as in the case when the remote mail server that the message is being delivered to is down. Or such as a misconfigured local mail server

**The **<mark style="color:red;">**`mailq`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command is used to query the mail messages queued for delivery.** To view the current list of messages in the queue, execute the command.

```bash
sysadmin@localhost:~$ mailq
-Queue ID- --Size-- ----Arrival Time---- -Sender/Recipient-------------
9E0C97F1A*      441 Thu Mar 27 10:02:09  sysadmin@localhost.localdomain
                                         test@bob.com
 
-- 0 Kbytes in 1 Request.
```

The fields in the output are described below:

| Field               | Significance                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Queue ID`          | <p>Queue File ID, which contains an <code>*</code> or <code>!</code> character</p><p>An <mark style="color:red;"><code>*</code> </mark> means the <strong>message is in the active queue</strong> and is waiting to be delivered (or re-sent)</p><p>An <mark style="color:red;"><code>!</code> </mark> means the <strong>message is on hold and no further delivery attempts will be made</strong></p> |
| `Size`              | Message Size                                                                                                                                                                                                                                                                                                                                                                                           |
| `Arrival Time`      | Timestamp when the message arrived in the mail queue                                                                                                                                                                                                                                                                                                                                                   |
| `Sender/Recipients` | Email IDs of the sender and the recipients to whom delivery is still pending                                                                                                                                                                                                                                                                                                                           |

{% hint style="info" %}
The mail behaviour of the <mark style="color:red;">`mailq`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> commands is identical to the <mark style="color:red;">`sendmail -bp`</mark> command for systems that are using the `sendmail` service.
{% endhint %}

To have **messages** in the mail queue **re-sent, use the **<mark style="color:red;">**`-q`**</mark>** option to the **<mark style="color:red;">**`mailq`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command**

## Aliasing Email Address

**Mail aliasing is a feature that allows alternative names (aliases) to be set up** so that when the alias is entered as the recipient name, the message will be sent to the email address or group of email addresses that the alias represents.

The <mark style="color:orange;">`/etc/mail/aliases`</mark> files defines the aliases. An alias can be created to an email address, a user name, a file, a command, or another alias. The entries in the file are in key-value format, as follows:

```
alias_name:  name_1, name_2, name_3
```

For example, to **deliver all messages that are sent to the local `support` mailbox to the development team** (`ted`, `jaime`, `olivia`, `ian`), place the following line in the `/etc/mail/aliases` file

```bash
sysadmin@localhost:~$ cat /etc/mail/aliases
support: ted, jaime, olivia, ian
```

> This will deliver messages meant for the `support` mailbox to the mailboxes for the specified users.

To **deliver all messages for a user to a set of alternaitve mailboxes**, place the following line in the `/etc/mail/aliases` file:

```bash
sysadmin@localhost:~$ cat /etc/mail/aliases
user1: user1@ind.pruebagroup.com; user1@coldmail.com
```

To send messages that are destined to the "applog" mailbox to a logging application(`/usr/local/bin/trackissues`) ,place the following line in the `/etc/mail/aliases` file:

```bash
sysadmin@localhost:~$ cat /etc/mail/aliases                                              
applog:  |/usr/local/bin/trackissues
```

To automatically discard email destined to system accounts (i.e `bin`) or a user account (i.e `boss`), redirect them to the `/dev/null` file in the `/etc/mail/aliases` file. For example:

```bash
sysadmin@localhost:~$ cat /etc/mail/aliases                                              
bin:  /dev/null
boss: /dev/null
```

{% hint style="info" %}
**Note**

The <mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> program, does not understand the format of the `/etc/mail/aliases` file, which is a flat data file. The <mark style="color:red;">**`sendmail`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** program reads a binary format** of the `/etc/mail/aliases` file, the <mark style="color:orange;">**`/etc/mail/aliases.db`**</mark> file. The <mark style="color:orange;">`aliases.db`</mark> file stores the records in database format along with indexes to facilitate faster lookups.

The `/etc/mail/aliases.db` file is created by the `newaliases` command from the data provided in the `/etc/mail/aliases` file. The `newaliases` command must be executed each time the `/etc/mail/aliases file` is updated to build the `aliases.db` database.

```bash
root@localhost:~ newaliases
```

The behaviour of the <mark style="color:red;">`newaliases`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is identical to the <mark style="color:red;">`sendmail -bi`</mark> command for systems that are using the <mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> program.
{% endhint %}

## Mail Forwarding

The `~/.forward` file, when placed in a user's home directory, is **used for automatically forwarding mail as it is received**. When a user receives an email, the MTA program checks the user's home directory for the `~/.forward` file. If the file exists, then the message is sent to the address(es) or alias(es) specified in this file.

A sample `~/.forward` file:

```bash
sysadmin@localhost:~$ cat ~/.forward                                              
support, psgsupport
```

This **will forward the incoming messages to the mailboxes** of `support` and `psgsupport` .The addresses or aliases specified should be valid addresses on the network. Once the message is forwarded, **a copy will not be retained in the user's mailbox.**

> Program names can also be specified in the `~/.forward` file. For example, the following will forward the incoming messages to the mailbox of `support` and to the `vacation` command:
>
> ```bash
> sysadmin@localhost:~$ cat ~/.forward                                              
> support, "|/usr/bin/vacation"
> ```
>
> The <mark style="color:red;">`vacation`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> utility is used to send auto-responses to mail.

One of the common uses of `~/.forward` file is to configure mail forwarding in situations when the user will be out of the office for an extended period. It is also possible to apply specific rules while forwarding if using the `exim` MTA.

> For example, if the subject contains the phrase `Production Problem` ,then the`exim` MTA could be configured to escalate the message and sent ot a certain set of recipients with high priority.

The `~/.forward` file should be deleted when the forwarding feature is no longer needed.

## SMTP Mail Protocol and Mail Transfer Agents

Simple Mail Transfer Protocol (SMTP) is the **** standard protocol for communication between email servers. Most email systems that send mail over the internet use **SMTP to send messages from one server to another**; the messages are then **retrieved with an email client using either POP3** (Post Office Protocol) **or IMAP** (Internet Message Access Protocol).

{% hint style="info" %}
Most Internet Service Providers (ISPs) provide both an SMTP server (such as `sendmail`) and a POP3 or IMAP server
{% endhint %}

SMTP can transfer mail over the same network or to some other network via a gateway. **It uses TCP/UDP port number 25 for communication**.

{% hint style="danger" %}
While configuring an email client, like Thunderbird, it is essential to specify the address of the SMTP server for outgoing mail.
{% endhint %}

The **Mail Transfer Agent (MTA) is a program on a server that is responsible for transferring and routing an email message** from the sender's computer to the recipient's computer. **MTAs use SMTP to transfer emails**.&#x20;

End-users use email clients, known as **Mail User Agents (MUAs) to retrieve, read, and send email.**

#### Mail Transfer Agents

There are many MTAs available, each with their own strengths and weaknesses. Some are easy to install and configure, some have strong security. **Microsoft Exchange** and **UNIX/Linux **<mark style="color:red;">**`sendmail`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> are two of the more common MTAs used today.&#x20;

Four of the most popular MTAs found on Linux systems are: <mark style="color:red;">`sendmai`</mark><mark style="color:red;">l</mark>, <mark style="color:red;">`postfix`</mark>, <mark style="color:red;">`qmail`</mark>, and <mark style="color:red;">`exim`</mark>.

{% hint style="warning" %}
The `qmail` MTA is mentioned for historical purposes in the even an administrator needs to administer an older Linux system.
{% endhint %}

### sendmail

The first version of <mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> was released in 1979 and was known as <mark style="color:red;">`delivermail`</mark>.  To adapt to the TCP protocol, `sendmail` was rewritten. At one point, `sendmail` was the standard MTA.

<mark style="color:red;">**`sendmail`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** uses DNS fro translating hostnames into their network addresses**. It is designed to transport messages between various types of systems such as Solaris, Linux, and AIX

<mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> has two major components: **the `sendmail` program** (referred to as the binary) and **the `sendmail` configuration file** (`/etc/mail/sendmail.cf` ) to allow for complex customization.

The <mark style="color:red;">`sendmail`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> configuration file is the core of its funciontality. It contains the information required for all tasks, such as locations of files, access permissions, and mode of operation. It also holds rules for converting mail addresses into formats required for delivery.

> The symbols used for writting the rules make it difficult for understanding and parsing the rules using scripts.

When a message arrives for delivery, it is processed as follows:

* If both the recepient and the sender are on the same machine, then `sendmail` delivers the message directly
* If the sender's and recepient's machines share a UUCP (Unix to Unix Copy) connection, then `sendmail` uses the `uux` program to delivery the message
* If the recepient's address is an internet address, then `sendmail` uses SMTP to deliver the message.

{% hint style="warning" %}
Since all messages cannot be delivered instantaneously, an intermediate storage location is required to hold messages for sending later. `sendmail` saves such messages in queues, which are files or directories on the file system. A message will be queued under the following conditions:

* `sendmail` can be configured to queue all messages by default to protect against message loss in case the system crashes
* If a message is intended for multiple recipients and delivery to some of the recipients fails, then the failed messages will be queued and retried again at a later time.
* If the destination machine is unreachable for any reason, then the message will be queued and scheduled for delivery only when the machine becomes available again.
{% endhint %}

The _header_ of a message is the most important component from the `sendmail` program's perspective. The `sendmail` program will analyze the header for routing information and, based on the rules in the configuration file, process the message.

**The **<mark style="color:red;">**`sendmail`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon manages the mail service**. The `/etc/mail/sendmail.cf` file is used to configure the `sendmail` daemon.

### sendmail Commnad

The `sendmail` facility, which includes the MTA components described above, also includes the `sendmail` command, which is a text-based email client used to send messages to recipients. **It handles the required routing to ensure correct delivery**.&#x20;

The main function of the `sendmail` command is to deliver pre-formatted messages. The `sendmail` command is an alternative to the simpler `mail` command.

Some of the key options of the `sendmail` command are:

| Option                                      | Meaning                                                                                                                                           |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">`-B type`</mark>   | Set the message’s body type to type, allowed values are `7BIT` or `8BITMIME`                                                                      |
| <mark style="color:red;">`-bd`</mark>       | Run in the background as a daemon                                                                                                                 |
| <mark style="color:red;">`-bD`</mark>       | Run as a foreground process                                                                                                                       |
| <mark style="color:red;">`-bi`</mark>       | Initialize the alias database                                                                                                                     |
| <mark style="color:red;">`-bp`</mark>       | List the mail queue                                                                                                                               |
| <mark style="color:red;">`-bv`</mark>       | Verify the address without sending an actual message                                                                                              |
| <mark style="color:red;">`-C file`</mark>   | Use the specified file as the configuration file                                                                                                  |
| <mark style="color:red;">`-R return`</mark> | Used when a message bounces. If set to `full`, then the entire message will be returned. If set to `hdrs`, then only the header will be returned. |
| <mark style="color:red;">`-t`</mark>        | Read message for recipients. The `To:`, `Cc:`, and `Bcc:` lines will be searched for valid recipient addresses.                                   |

While many end-users will uses graphical-based email programs like Evolution. Thunderbird, and Pine or text-based programs like `mail`, `mailx`, `mutt` to send mail, a system administrator will use the `sendmail` command to test the functionality of the `sendmail` daemon.

For example, to send mail to the `root` user on the local system, execute the following:

```bash
root@localhost:~ sendmail root@localhost
```

After entering the previous command, the cursor is placed on a blank line where the message can be entered via standard input (keyboard). **To send the message, enter a `.` (period) on a new line** and press the **Enter** key.

```
From: sysadmin@localhost To: root@localhost This is a test message!
.
```

Message contents can be specified in a file and read by `sendmail` instead of typing manually like the example above. To use this method, create a file (i.e `sendmail.msg1`) with the following contents:

```bash
From: sysadmin@localhost 
To: root@localhost 
Subject: Test
 
This is a test message!
```

To process this file, execute the following command:

```bash
root@localhost:~ sendmail -t -i < sendmail.msg1
```

This will read the `To:` line from the file and send the message to the specified recipient(s).

### Postfix

A limitation of the `sendmail` email program is its monolithic design. Many functins, such as queuing mail, interpreting `.forward` files, retrying mail and attempting SMTP deliveries are handled by a single executable, which causes potential security issues since the executable is owned by the `root` user

Generally, Red Hat-based systems have two MTAs available, `postfix` and `sendmail`, with the default being `postfix` .Only one MTA should be running at a time.

In the 1990s, the `postfix` program was developed at IBM as an alternative to `sendmail`. **The main advantages of the **<mark style="color:red;">**`postfix`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** program are its enhanced security and ease of administration.**

{% hint style="info" %}
The `postfix` program runs in a router-like mode where it accepts messages from different sources (such as local users and SMTP), support lookups in its maps to decide where to send messages, and applies any rules if specified on the message.
{% endhint %}

The `master` daemon oversees the operations of other `postfix` daemons. The queue manager daemon delegates delivery tasks to the local and SMTP daemons. It maintains a limited number of messages in the active queue and keeps the remaining messages in the deferred queue. **The **<mark style="color:red;">**`showq`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** daemon list the mail queue to provide the information required by the **<mark style="color:red;">**`mailq`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command.**

The configuration file of the `postfix` program can be updated easily using the Postfix Admin web-based program.

The <mark style="color:red;">`postfix`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> program provides a faster, secure, and easy to use alternative to <mark style="color:red;">`sendmail`</mark>

### `exim`

In 1995, anotehr alternative to the `sendmail` program, know as the `exim` program was developed. Like other alternatives to `sendmail` the main focus of the `exim` program was better security.

The `exim` program follows a single binary for all functions design, similar to the `sendmail` program. But the `exim` program has an improved implementation with a focus on security. Also, it provides many features such as access control lists, for better policy control, integration with spam and virus scanners ,and managing  delivery issues with retry rules.

The creators of the `exim` program opted to have the majority of the messages delivered immediately as long as there are no errors. So they tried to implement a mechanism where the message delivery time was reduced by doing away with a queuing mechanism.

