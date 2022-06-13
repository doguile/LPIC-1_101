---
description: '108.3: Mail Transfer Agent (MTA) basics v2  Weight: 3'
---

# Chapter 11: Email Configuration

<details>

<summary>Key terms</summary>

`exim` Developed in 1995 as another alternative to the sendmail program.

`mail` This commnad is built-in-text-based MUA for Linux that does not support attachments. The mail command interface used to send, read, reply and delete mail. `mail` is handy for administrators to send/receive mail when a graphical interface is not running of for simple email testing.

`mailq` Command used to query the mail messages queued for delivery

`newaliases` Command used to create and update the forwarding database (/etc/mail/aliases.db) accessed by sendmail using the contents of the /etc/mail/aliases flat data file

`postfix` Alternative to sendmail. The main advantages of the postfix program is its enhanced security and ease of administration

`sendmail` Is a general purpose internetwork email routing facility that supports many kinds of mail-transfer and delivery methods, including the SMTP used for email transport over the internet. sendmail has two major components: the sendmail program and the sendmail configuration file to allow for complex customization.

`sendmail emulation layer commands` A set of commands the administrator uses that emulate sendmail in order to test the sendmail daemon is functioning properly

`~/.forward` When placed in a user's home directory, .forward is used for automatically forward mail to the addresses or aliases specified in this file.

</details>

## Introduction

