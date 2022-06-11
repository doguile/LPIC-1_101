---
description: '103.1: Work on the command line v2  Weight: 4'
---

# Chapter 2: Using the shell

<details>

<summary>Key terms</summary>

**`Quoting`** Enclosing special characters in quotes will prevent the shell from interpreting special  characters.

* _Double qoutes_ will prevent the shell from interpreting some of these special characters.
* _Single quotes_ prevent the shell from interpreting any special characters

**`echo`** Echo the STRING(s) to standard output. Useful with scripts

**`man`** An interface to the on-line reference manuals

**`pwd`** Print the name of the current working directory

**`uname`** Print certain system information such as kernel name, network node hostname, kernel release, kernel version, etc.

</details>

## Command Line Interface

By learning the CLI, a user can easily be productive almost instantly on ANY flavor or distribution of Linux, reducing the amount of time needed to familiarize themselves with a system because of variations in a GUI.

## Commands

A command is a software program that when executed on the command line, performs an action on the computer.

When you consider a command using this definition, you are really considering what happens when you execute a commnad. When you type in a command, a process is run by the operating system that can read input, manipulate data, and produce output. From this perspective, **a command runs a process on the operating system**, which then causes the computer to perform a _**`job`**_

However, there is another way of looking at what a command is: look at its source. The source is where the command "comes from" and there are several different sources of commands within the shell of your CLI:

* **Internal Commands:** Also called _**built-in**_** commands**, these commands are built-in to the shell itself. An example is the <mark style="color:red;">`cd`</mark> command as it is part of the Bash shell.
* **External Commands:** These commands are stored in files that are searched by the shell. If you type the _<mark style="color:red;">`ls`</mark> _ command, then the shell searches through a predetermined list of directories to try to find a file named <mark style="color:red;">`ls`</mark> that it can execute. _These commands can also be executed by typing the complete path to the command._
* **Aliases:** An alias can override a bulit-in command, function or a command that is found in a file. Aliases can be useful for creating new commands built from existing functions and commands.
* **Functions:** Functions can also be built using existing commands to either create new commands, override commands built-in to the shell or commands stored in files.

### External Commands

Commands that are stored in files can be in several forms that you should be aware of. Most commands are written in the C programming language.

{% hint style="warning" %}
It is possible to view available software packages, binary programs that can be installed directly at the command line. To view the available source package for the GNU Compiler Collection:

`apt-cache search gcc | grep source`
{% endhint %}

The <mark style="color:red;">**`apt-cache`**</mark> commands **allows us to display information from the APT database cache**. It is commonly used to find information about programs you wish to install and the components required to make them work.

{% hint style="warning" %}
The Free Software Foundation (FSF) distributes the GNU Compiler Collection (GCC) to make this process easier. **The GCC provides a compiler system** (the special programs used to convert source code into usable binary programs) **with front ends for many different programming languages.**
{% endhint %}

### Aliases

An alias can be used to map longer commands to shorter key sequences.

For example, the command <mark style="color:red;">`ls- l`</mark> is commonly aliased to <mark style="color:red;">`l`</mark> or <mark style="color:red;">`ll`</mark> .Because these smaller commands are easier to type.

To determine what aliases are set on the current shell use the <mark style="color:red;">`alias`</mark> command.

```bash
sysadmin@localhost:~$ alias
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
```

New aliases can be created using the following format, where <mark style="color:red;">`name`</mark> is the name to be given the alias and _command_ is the command to be executed when the alias is run:

```bash
alias name="command"
alias mycal="cal 2030"
```

**Aliases created this way only persist while the shell is open.** Once the shell is closed, the new aliases are lost.&#x20;

{% hint style="danger" %}
Each shell has its own aliases, so, aliases created in one shell won't be available in a new shell that's opened.
{% endhint %}

You can **remove entries from your list of aliases by using the **<mark style="color:red;">**`unalias`**</mark>**command**

## Basic Commands Syntax

Keep it mind that every part of the command is normally case-sensitive, so <mark style="color:red;">**`LS`**</mark>** is incorrect** and will fail, but <mark style="color:red;">`ls`</mark> is correct and will succeed.

### Specifying arguments

```
command [options] [arguments]
```

An _`argument` _ can be used to specify something for the command to act upon. Any desired arguments are allowed or are required depending on the command.

&#x20;For example, the <mark style="color:red;">**`touch`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command is used to **create empty files or update the timestamp of existing files**. It requires at least one argument.

```bash
touch newfile
```

The <mark style="color:red;">`ls`</mark>command, allows for a path and/or file name to be specified as an argument, but it's not required.

Some commands, like the <mark style="color:red;">`cp`</mark> command and the <mark style="color:red;">`mv`</mark> command, always require at least two arguments: a source file and a destination file

```bash
cp SOURCE DESTINATION
```

```bash
sysadmin@localhost:~ cp /etc/ssh/ssh_host_rsa_key.pub /home/sysadmin/Documents
```

{% hint style="info" %}
An _ssh rsa key_ is a file that contains an authentication credential (similar to a password) used to verify the identity of a machine that is being logged into using the <mark style="color:red;">`ssh`</mark> command.
{% endhint %}

### Quoting

**Arguments that contain unsual characters** like spaces or non-alphanumeric characters will usually **need to be **_**quoted**,_ either by enclosing them with double quotes or single quotes.

* _**Double quotes**_ will prevent the shell from interpreting some of these special characters
* **S**_**ingle quotes**_ prevent the shell from interpreting any special characters.

Consider you want to list the contents of the current directory using the <mark style="color:red;">`ls`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command and then use the <mark style="color:red;">`echo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command to display the string `Hello World!!` on the screen.

```bash
sysadmin@localhost:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos

sysadmin@localhost:~$ echo hello world!!
echo hello worldls
hello worldls
```

Using no quotes will failed because the shell interprets the <mark style="color:red;">`!!`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> characters as special characters; in this case, they mean "**replace the **<mark style="color:red;">**`!!`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** with the last command that was executed**". In this case, the last command was the <mark style="color:red;">`ls`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, so <mark style="color:red;">`ls`</mark> replaced <mark style="color:red;">`!!`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> and then the <mark style="color:red;">`echo`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command displayed hello wordls to the screen.

```bash
echo "Hello world!!"
```

Using **double quotes preserves the literal value of all characters** that they enclose **except metacharacters** such as:

* The <mark style="color:red;">`$`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> dollar sign character
* The <mark style="color:red;">`´`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> bakcquote character
* The <mark style="color:red;">`\`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> backslash character
* The <mark style="color:red;">`!`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> exclamation point character.&#x20;

These characters **(**<mark style="color:red;">**`$ \ !`**</mark>**),** called **wildcards,** are **used for **_**globbing**,_ and are interpreted by the shell itself before it attempts to run any command.

> **Glob characters are useful because they allow you to specify patterns that make it easier to match file names in the command line.**

If you enclose text within the <mark style="color:red;">`'`</mark> **single quote characters**, then **all characters have their literal meaning.**

```bash
echo 'hello world!!'
hello world!!
```

### Options

_Options_ can be used with commands to **expand or modify the way a command behaves**. If it is necessary to add options, they can be specified after the command name. Short options are specified with a hypen <mark style="color:red;">`-`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> followed by a single character.

```bash
command [options] [arguments]

sysadmin@localhost:~$ ls -l
```

By default, the <mark style="color:red;">`ls`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command prints the results in alphabetical order, so adding the <mark style="color:red;">`-r`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> options prints the results in reverse alphabetical order.

> In most cases, _options_ can be used in conjunction with other options. They can be given as separate options like <mark style="color:red;">`-l -r`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> or combined like <mark style="color:red;">`-lr`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> .**The output of all these examples would be the same**

{% hint style="warning" %}
The expection to this is when an options requires an argument.
{% endhint %}

For example, the <mark style="color:red;">**`-w`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> options to the <mark style="color:red;">**`ls`**</mark>** ** command specifies the _width_ of the output desired and therefore requires an argument. If combined with other options, the <mark style="color:red;">`-w`</mark> option can be specified last, followed by its argument and still be valid**,** as in <mark style="color:red;">`ls -rtw 40`</mark>, which specifies an output width of 40 characters. Otherwise, the `-w` option cannot be combined with other options and must be given separately.

```bash
sysadmin@localhost:~$ ls -lr -w 40                                            
total 32                                                                        
drwxr-xr-x 2 sysadmin sysadmin 4096 Feb 22 16:32 Videos                    	 
drwxr-xr-x 2 sysadmin sysadmin 4096 Feb 22 16:32 Templates                 	 
drwxr-xr-x 2 sysadmin sysadmin 4096 Feb 22 16:32 Public                    	 
drwxr-xr-x 2 sysadmin sysadmin 4096 Feb 22 16:32 Pictures                  	 
drwxr-xr-x 2 sysadmin sysadmin 4096 Feb 22 16:32 Music   
```

{% hint style="danger" %}
If you are using multiple options that require arguments, don't combine them.
{% endhint %}

For example, the <mark style="color:red;">**`-T`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> option, which specifies _tab size,_ also requires an argument. In order to accommodate both argumnets, each option is given separately.

```bash
sysadmin@localhost:~$ ls -w 40 -T 12
Desktop	Music 	Templates
Documents  Pictures  Videos
Downloads  Public
```

Some commands support additional options that are longer than a single character. _Long options_ for commands are preceded by a double hyphen `--` and the meaning of the option is typicaly the name of the option, like the <mark style="color:red;">`--all`</mark> option, which list all files, including hidden ones.

```bash
sysadmin@localhost:~$ ls --all
.         	.bashrc   .selected_editor  Downloads  Public	 
..        	.cache	Desktop       	Music  	Templates
.bash_logout  .profile  Documents     	Pictures   Videos
```

For commands that support both long and short options, execute the command using the long and short options concurrently.

```bash
sysadmin@localhost:~$ ls --all --reverse -t
.profile  	Templates  Music  	Desktop       	..                   	 
.bash_logout  Public 	Downloads  .selected_editor  .cache               	 
Videos    	Pictures   Documents  .bashrc       	.
```

**Commands that support long options will often also support arguments that may be specified with or without an equal symbol** (the output of both commands is the same):

```bash
ls --sort time
ls --sort=time
```

```bash
sysadmin@localhost:~$ ls --sort=time
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

A special options exists, the lone _<mark style="color:red;"></mark>_ double _<mark style="color:red;"></mark>_ hyphen <mark style="color:red;">`--`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> option, which can be used to **indicate the end of all options for the command.** For example, if the `touch` command tries to create a file called   `--badname` :

```bash
sysadmin@localhost:~$ touch --badname
touch: unrecognized option '--badname'
Try 'touch --help' for more information.
```

The command tries to interpret `--badname` as an option instead of an argument. However, if the lone double hyppen `--` options is placed befor the file name, indicating that there are no more options, then the file name can successfully be interpreted as an argument.

```bash
sysadmin@localhost:~$ touch -- --badname
sysadmin@localhost:~$ ls
--badname  Documents  Music 	Public 	Videos                          	 
Desktop	Downloads  Pictures  Templates
```

The ignore <mark style="color:red;">`-I`</mark> <mark style="color:red;"></mark>_<mark style="color:red;"></mark>_ option will cause the <mark style="color:red;">`ls`</mark> command to not display specific files that match a pattern.

```bash
sysadmin@localhost:~$ ls -I "D*"                                                
Music  Pictures  Public  Templates  Videos
```

## Scripts

One exception to the basic command syntax used is the <mark style="color:red;">`exec`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command, which **takes another command to execute as an argument.**

> A  common use of the <mark style="color:red;">`exec`</mark>command is in what is known as _wrapper scripts._ If the purpose of a script is to simply configure and launch another program, then it is known as a wrapper script.

A _wrapper script_ often uses the following as the **last line of the script** to execute another program.

```bash
exec program
```

A script written this way avoids having a shell continue to run while the program that it launched is running, the result is that this technique saves resources (like RAM).

{% hint style="warning" %}
**the **<mark style="color:red;">**`exec`**</mark>** command can be used to cause redirection for one or more statements in a script**
{% endhint %}

## Displaying System Information

The <mark style="color:red;">`uname`</mark> command **displays system information**. The <mark style="color:red;">`uname`</mark> command is useful for several reasons, including when you need to determine the name of the computer as well as the current version of the kernel that is being used.

For example, to display all the information about the system, use the <mark style="color:red;">`-a`</mark> option with the <mark style="color:red;">`uname`</mark> command.

```bash
sysadmin@localhost:~$ uname -a
Linux localhost 4.4.0-72-generic #93~14.04.1-Ubuntu SMP Fri Mar 31 15:05:15 UTC 
2017 x86_64 x86_64 x86_64 GNU/Linux
```

The options for the `uname` command are summarized below:

| Short Option | Long Option           | Prints                       |
| ------------ | --------------------- | ---------------------------- |
| `-a`         | `--all`               | All information              |
| `-s`         | `--kernel-name`       | Kernel name                  |
| `-n`         | `--node-name`         | Network node name            |
| `-r`         | `--kernel-release`    | Kernel release               |
| `-v`         | `--kernel-version`    | Kernel version               |
| `-m`         | `--machine`           | Machine hardware name        |
| `-p`         | `--processor`         | Processor type or unknown    |
| `-i`         | `--hardware-platform` | Hardware platform or unknown |
| `-o`         | `--operating-system`  | Operating system             |
|              | `--help`              | Help information             |
|              | `--version`           | Version information          |

## Current Directory

One of the simplest commands available is the <mark style="color:red;">`pwd`</mark> command, which is an acronym for _print working directory_.

```
sysadmin@localhost:~$
```

In the first prompt above, the blue tilde `~` character is equivalent to `/home/sysadmin`, **representing the user's home directory.**

## Command Information

The <mark style="color:red;">**`type`**</mark>command **displays information about a command type**. For example, if you entered <mark style="color:red;">**`type ls`**</mark>** ** at the command prompt, it will return that the <mark style="color:red;">`ls`</mark> command is actually an alias for the <mark style="color:red;">`ls --color=auto`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command:

```bash
sysadmin@localhost:~$ type ls                                                   
ls is aliased to `ls --color=auto'
```

Using the <mark style="color:red;">**`-a`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> option with the <mark style="color:red;">**`type`**</mark> command will **return all locations of the files that contain a command**; also called an _executable_ file:

```bash
sysadmin@localhost:~$ type -a ls                                                
ls is aliased to `ls --color=auto'                                              
ls is /bin/ls
```

> In the output above, the `/bin/ls` file path is the file location of the `ls` command.

For **internal commands**, like the <mark style="color:red;">`pwd`</mark> command, the <mark style="color:red;">`type`</mark> command will identify them as **shell builtins:**

```
sysadmin@localhost:~$ type pwd                                                  
pwd is a shell builtin
```

For **external commands** like the <mark style="color:red;">`ip`</mark> command, the <mark style="color:red;">`type`</mark> command will **return the location of the command**, in this case, the `/sbin` directory

```bash
sysadmin@localhost:~$ type ip                                                   
ip is /sbin/ip
```

{% hint style="danger" %}
The <mark style="color:red;">`/bin`</mark>directory contains executable programs needed for **booting a system**, commonly used commands, and other **programs needed for basic system functionality**

The <mark style="color:red;">`/sbin`</mark>directory also contains executable programs; mainly commands and **tools designed for system administration**
{% endhint %}

If a command does not behave as expected or if a command is not accessible, that should be, it can be beneficial to know where the shell is **finding the command**.

The <mark style="color:red;">**`which`**</mark> command **searches for the location of a command** in the system **by searching the `PATH` ** variable.

```bash
sysadmin@localhost:~$ which ls                                       
/bin/ls
```

{% hint style="info" %}
The `PATH` variable contains a list of directories that are **used to search for commands entered by the user**.
{% endhint %}

## Getting help

As previously mentioned, UNIX was the operating system from which the foundation of Linux was built. The developers of UNIX created help documents called man pages (short for manual page).

Referring to the man page for a command will provide you with the basic idea behind the purpose of the command, as well as details regarding the options of the command and a description of its features.

### Viewing Man Pages

To view a man page for a command, execute the <mark style="color:red;">`man`</mark> command in a terminal windows.

```
man command
```

### Controlling the Man Page Display

The <mark style="color:red;">`man`</mark> command uses a pager to display documents. Typically, this pager is the <mark style="color:red;">`less`</mark> command, but on some distributions, it may be the <mark style="color:red;">`more`</mark> command. Both are very similar in how they perform.

If you use the <mark style="color:red;">`less`</mark> command, you might be a bit overwhelmed with the large number of "commands" that are available. The following table provides a summary of the more useful commands:

| Command           | Function                          |
| ----------------- | --------------------------------- |
| Return (or Enter) | Go down one line                  |
| Space             | Go down one page                  |
| _`/term`_         | Search for _term_                 |
| n                 | Find next search item             |
| Shift +N          | Return previous match of the term |
| `1G`              | Go to the beginning of the page   |
| `G`               | Go to the end of the page         |
| `h`               | Display help                      |
| `q`               | Quit man page                     |

### Man Pages Synopsis

The `SYNOPSIS` section of a man page is a valuable resource since it **provides a concise example of how to use the command**.

```
SYNOPSIS
     cal [-3hjy] [-A number] [-B number] [[month] year]
```

The square brackets `[ ]` are used to indicated that this feature is **not required to run the command**. For example, `[-3hjy]` means you can use the options `-3`, `-h`, `-j`, `-y`, but none of these options are required for the `cal` command to function properly.

The second set of square brackets `[-A number]` allows you to ‌⁠​​⁠specify the number of months to be added to the end of the display.

The second set of square brackets `[-A number]` allows you to ‌⁠​​⁠specify the number of months to be added to the end of the display.

The fourth set of square brackets in the `[[month] year]` demonstrates another feature; it means that you can specify a year by itself, but if you specify a month you must also specify a year.

Another component of the `SYNOPSIS` that might cause some confusion can be seen in the `SYNOPSIS` of the `date` command:

```
SYNOPSIS                                                                        
       date [OPTION]... [+FORMAT]
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
```

In this `SYNOPSIS` there are two syntaxes for the <mark style="color:red;">`date`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command. The first one is used to display the date on the system while the second is used to set the date.

The ellipses `...` following `[OPTION]`, indicate that `[OPTION]` may be repeated.

Additionally, the `[-u|--utc|--universal]` notation means that you can either use the `-u` option or the `--utc` option, or the `--universal` option.

### Man Page Sections

To organize all of these man pages, the pages are categorized by sections, much like each individual man page is broken into sections.

By default, there are **nine** **sections** of man pages:

1. Executable programs or shell commands
2. System calls (functions provided by the kernel)
3. Library calls (functions within program libraries)
4. Special files (usually found in `/dev`)
5. File formats and conventions, e.g. `/etc/passwd`
6. Games
7. Miscellaneous (including macro packages and conventions), e.g. `man(7)`, `groff(7)`
8. System administration commands (usually only for root)
9. Kernel routines \[non-standard]

### Determining Which Section

To determine which section a specific man page belongs to, look at the numeric value on the first line of the output of the man page.

![](<../.gitbook/assets/imagen (1) (1).png>)

### Specifying a Section

In some cases, you will need to specify the section in order to display the correct man page. This is necessary because sometimes there will be man pages with the same name in different sections.

To specify a different section, provide the number of the section as the first argument of the <mark style="color:red;">`man`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command. For example, the command <mark style="color:red;">`man 5`</mark>` ``passwd` will look for the `passwd` man page in section 5 only.

### Searching by Name

The <mark style="color:red;">`-f`</mark> <mark style="color:red;"></mark><mark style="color:red;">****</mark> option to the <mark style="color:red;">`man`</mark> <mark style="color:red;"></mark><mark style="color:red;">****</mark> command will display man pages that **match, or partially match, a specific name** and **provide a brief description** of each man page:

```bash
sysadmin@localhost:~$ man -f passwd
passwd (1ssl)        - compute password hashes
passwd (1)           - change user password
passwd (5)           - the password file
```

{% hint style="info" %}
Note that on most Linux distributions, the <mark style="color:red;">**`whatis`**</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command **does the same thing** **as** <mark style="color:red;">**`man -f`**</mark>. On those distributions, both will produce the same output.
{% endhint %}

### Searching by Keyword

You can search for man pages that **match a keyword** by using the <mark style="color:red;">**`-k`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> option to the <mark style="color:red;">**`man`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark>** command**.

```
sysadmin@localhost:~$ man -k password
chage (1)            - change user password expiry information
chgpasswd (8)        - update group passwords in batch mode
chpasswd (8)         - update passwords in batch mode
cpgr (8)             - copy with locking the given file to the password or gr...
cppw (8)             - copy with locking the given file to the password or gr..
```

{% hint style="info" %}
Note that on most Linux distributions, the <mark style="color:red;">**`apropos`**</mark> command **does the same thing** **as** <mark style="color:red;">**`man -k`**</mark>. On those distributions, both will produce the same output.
{% endhint %}
