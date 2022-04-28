# Module 1: Advanced Shell Features

## Chapter 1: Advanced Shell Features

> **105.1: Customize and use the shell environment v2**
>
> Weight: 4

<details>

<summary>key terms</summary>

`&&` This is an AND list. If the command on the left side of `&&` does execute successfully, then the command on the right of `&&` will execute

`.` Command used to re-execute a bash shell configuration file (.profile, .bashrc, etc) after changes are made

`/etc/bash.bash.rc` Systemwide configuration file read when a new shell starts. Contains variables, commands, and aliases common to all users.

`/etc/profile` Systemwide bash configuration file read at login. Contains bash settings and environment variables common to all users.

`alias` Alias and unalias utilities create or remove a nickname for a command or series of commands.

<mark style="color:red;">`env`</mark> Displays all environmental variables available to all shells.

<mark style="color:red;">`export`</mark> With no arguments displays all environmental variables to all shells. export `VARIABLE` is used to export a local variable.

<mark style="color:red;">`function`</mark> SImilar to an alias but for executing multiple commands. Typically used in bash shell scripts.

<mark style="color:red;">`set`</mark> with no arguments `set` displays the values of all variables (local and enviromental) set in the current shell

<mark style="color:red;">`source`</mark> Command used to re-execute a bash shell configuration file (.profile, .bashrc, etc.) after changes are made.

<mark style="color:red;">`unset`</mark> Removes the variable from the current shell and subshells

`||` This is an OR list. If the command on the left side of `||` does NOT execute successfully, then the command on the right side of `||` is executed.

`~/.bash_login` A bash user configuration file used to customize the shell enviroment.

`~/.bash_logout` A bash user configuration file read at logout. Typically contains "clean up" command such as clear to remove any text on the screen

`~/.bash_profile` A bash user configuration file used to customize the shell environment.

`~/.bashrc` A bash user configuration file read each time a new shell is started. Typically contains local variables, commands and aliases.

`~/.profile` A bash user configuration file used to customize the shell environment.

</details>

## Introduction

This chapter will address shell features and shell programming. Understanding how to customize a shell environment is an essential skill for both end-users and adminstrators. End-users may want to modify their shell environment to suit their specific needs.

Administrators can make the shell environment easier to use for entire sets of users.

## Shell Variables

**A variable is a name or identifier that can be assigned a value**. The shell (and other commands) reads the values of these variables, which can result in altered behaviour depending on the contents(value) of the variable.

Variables typically hold a single value, like `0` or `bob` .Some may contain multiple values separated by spaces like `Joe Brown` or by other characters, such as colons:  `/usr/bin:/usr/sbin:/bin:/usr/bin:/home/joe/bin.`

You assign a value to a variable by typing the name of the variable, immediately followed by an equal sign `=` and then the value. For example:

```bash
name="Bob Doe"
```

Variables names should **start with a letter** (alpha character) **or underscore** and **contain only letters, numbers, and the underscore character**.

{% hint style="info" %}
Variable names are case-sensitive
{% endhint %}

Just as with arguments to commands, single or double quotes should be used when special characters are included in the value assigned to the variable to prevent shell expansion.

| Valid Variable Assignments | Invalid Variable Assignments |
| -------------------------- | ---------------------------- |
| `a=1`                      | `1=a`                        |
| `_1=a`                     | `a-1=3`                      |
| `LONG_VARIABLE='OK'`       | `LONG-VARIABLE='WRONG'`      |
| `Name='Jose Romero'`       | `'user name'=anything`       |
| `sshdirectory='/etc/ssh'`  | `"sshdirectory"=etc/ssh`     |

The Bash shell and many commands make extensive use of variables. The Bash shell and commands can behave in different ways, based on the value of variables. For the shell, variables can affect what the prompt displays, the directories the shell will search for commands to execute, and much more.

### Local and Environment Variables

A _local_ variable is only available to the shell in which it was created. An _environment_ variable is available to the shell in which it was created, and it is passed into all other commands/programs started by the shell.

> By convention, lowercase characters are used to create local variables names, and uppercase characters are used when naming an environment variable.

There are several ways to display the values of variables. The <mark style="color:red;">**`set`**</mark> **command** by itself will **display all variables (local and environment).**

```bash
sysadmin@localhost:~$ set | head
BASH=/bin/bash                                                                  
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote
:force_fignore:histappend:interactive_comments:login_shell:progcomp:promptvars:s
ourcepath                                                                       
BASH_ALIASES=()                                                                 
BASH_ARGC=()                                                                    
BASH_ARGV=()                                                                    
BASH_CMDS=()                                                                    
BASH_COMPLETION_VERSINFO=([0]="2" [1]="8")                                      
BASH_LINENO=()                                                                  
BASH_SOURCE=()                          
```

To displau only environment variables, there are several commands that provide nearly the same output: <mark style="color:red;">`env`</mark>, <mark style="color:red;">`declare -x`</mark>, <mark style="color:red;">`typeset -x`</mark>, or <mark style="color:red;">`export -p`</mark>

```bash
sysadmin@localhost:~$ declare -x | tail -5                                      
declare -x PWD="/home/sysadmin"                                                 
declare -x SHELL="/bin/bash"                                                    
declare -x SHLVL="1"                                                            
declare -x TERM="xterm"                                                         
declare -x USER="sysadmin"              
sysadmin@localhost:~$ typeset -x | tail -5                                      
declare -x PWD="/home/sysadmin"                                                 
declare -x SHELL="/bin/bash"                                                    
declare -x SHLVL="1"                                                            
declare -x TERM="xterm"
declare -x USER="sysadmin"     
sysadmin@localhost:~$ export -p | tail -5                                       
declare -x PWD="/home/sysadmin"                                                 
declare -x SHELL="/bin/bash"                                                    
declare -x SHLVL="1"                                                            
declare -x TERM="xterm"                                                         
declare -x USER="sysadmin"as
```

To display the value of a specific variable, use the <mark style="color:red;">`echo`</mark> command with the name of the variable prefixed by the dollar <mark style="color:red;">`$`</mark> sign. For example, to display the value of the `PATH` variable, you would execute `echo $PATH`

```bash
sysadmin@localhost:~$ echo $PATH
/home/sysadmin/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:
/usr/games:/usr/local/games
```

{% hint style="warning" %}
**Variables can also be enclosed in curly `{}` braces** in order to delimit them from surrounding text.
{% endhint %}

## Creating Environment Variable

By default, when a variable is assigned in the Bash shell, it is initially set as local variable. There are a few ways that a local variable can also be made to be an environment variable. First, an existing local variable can be exported with the <mark style="color:red;">`export`</mark> command:

```bash
sysadmin@localhost:~$ ENVIRONMENT_A=1
sysadmin@localhost:~$ export ENVIRONMENT_A
```

Second, a new variable can be _exported_ and assigned a value with a single command:

```bash
sysadmin@localhost:~$ export ENVIRONMENT_B=2
```

Third, the `declare` or `typeset` command can be used, to declare a variable to be an enviroment variable. These commands are synonymous and work the same way.

```bash
sysadmin@localhost:~$ declare -x ENVIRONMENT_C=3
sysadmin@localhost:~$ typeset -x ENVIRONMENT_D=3
```

Using the following syntax, the `env` command can also be used to temporarily set a variable:

```
env VARIABLE_NAME=TEMP_VALUE command
```

To temporarily set the time zone variable `TZ` to Eastern Standard Time (EST), use the `env` command:

```bash
sysadmin@localhost:~$ env TZ=EST date
Fri Sep 18 16:27:54 EST 2020
```

## Unsetting Variables

If the shell option <mark style="color:red;">`nounset`</mark> is enabled with the <mark style="color:red;">`set -o nounset`</mark> command, then referring to an unset variable will result in an `unbound variable` error. When the command is used within a script, referencing an unset variable will cause the script to exit.

This option may be turned off, using the <mark style="color:red;">`set +o nounset`</mark> command, where any reference to an unset variable will return a null value.

If you create a variable and then no longer want that variable to be defined, use the <mark style="color:red;">`unset`</mark> command to delete it.

```bash
sysadmin@localhost:~$ variable_1="This is a variable." 
sysadmin@localhost:~$ echo $variable_1
This is a variable.
sysadmin@localhost:~$ unset variable_1
sysadmin@localhost:~$ set -o nounset
sysadmin@localhost:~$ echo $variable_1
-bash: variable_1: unbound variable
sysadmin@localhost:~$ set +o nounset
sysadmin@localhost:~$ echo $variable_1
 
sysadmin@localhost:~$
```

## Understanding the PATH variable

The `PATH` variable is one of the most critical environment variables for the shell.

The following graphic displays a typical `PATH` variable, with directory names separated from each other by the colon `:` character:

```bash
sysadmin@localhost:~$ echo $PATH
/home/sysadmin/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:
/usr/games:/usr/local/games
```

The `PATH` variable contains a list of directories that are used to search for commands entered by the user.

{% hint style="info" %}
Before searching the `PATH` variable for the command, the shell will first determine if the command is an alias or function, which may result in the `PATH` variable not being utilized when that specific command is executed.

Additionally, if the command happens to be built-in to the shell, the `PATH` variable will not be utilized.

Lastly, if the user uses an absolute path such as `/bin/ls` or a relative path such as `./ls` ,then the `PATH` variable is not utilized.
{% endhint %}

The following table describes the purpose of some of the directories displayed in the `PATH` variable above:

| Directory            | Contents                                                                                                             |
| -------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `/home/sysadmin/bin` | A directory for the current user `sysadmin` to place programs. Typically used by users who create their own scripts. |
| `/usr/local/sbin`    | Normally empty, but **may have administrative commands** that have been compiled from local sources.                 |
| `/usr/local/bin`     | Normally empty, but **may have commands that have been compiled from local sources**.                                |
| `/usr/sbin`          | Contains the **majority of the administrative command** files.                                                       |
| `/usr/bin`           | Contains the **majority of the commands that are available for regular users** to execute.                           |
| `/sbin`              | Contains the **essential administrative commands.**                                                                  |
| `/bin`               | Contains the **most fundamental commands that are essential for the operating system** to function.                  |

To execute commands that are not contained in the directories that are listed in the `PATH` variable, several options exist:

* The command may be executed by typing the absolute path to the command
* The command may be executed with a relative path to the command
* The `PATH` variable can be set to include the directory where the command is located.
* The command can be relocated or copied to a directory that is listed in the `PATH` variable.

### Modify `PATH` Variable

It is possible to add the `PATH` variable without overwriting its previous contents. **Import the current value of the **<mark style="color:red;">**`$PATH`**</mark>** variable into the newly-defined `PATH` variable** by using it on both sides of the assignment statement.

```
sysadmin@localhost:~$ PATH=$PATH
```

Finish it with the value of the additional home directory path.

```bash
sysadmin@localhost:~$ PATH=$PATH:/home/sysadmin
sysadmin@localhost:~$ echo $PATH
/home/sysadmin/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/sysadmin
```

Now, scripts located in the `/home/sysadmin` directory can execute without using a path:

```bash
sysadmin@localhost:~$ my.sh
Hello World!
```

{% hint style="info" %}
How can you tell if a command that you are creating might have the same name as another command on the system? Given that the conflict could be with a regular command, another shell script, a built-in command, an alias, or a function, it could be a bit challenging. Fortunately, the <mark style="color:red;">`type`</mark> command will help you discover any potential conflicts.

```bash
sysadmin@localhost:~$ type echo
echo is a shell builtin
sysadmin@localhost:~$ type ls
ls is aliased to `ls --color=auto'
sysadmin@localhost:~$ type cal
cal is /usr/bin/cal
```
{% endhint %}

## Additional Common Variables

Typically, the prompt includes the user name, hostname, and current directory:

```bash
sysadmin@localhost:~$
```

This prompt can be changed by modifying the `PS1(Promp Shell 1)` variable. Before making any changes to the `PS1` ,back it up using a local variable so it can be restored to its original state:

```bash
sysadmin@localhost:~$ echo $PS1                                                 
\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033
[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$   
sysadmin@localhost:~$ ps1=$PS1
```

There are several useful special character sequences available to customize this prompt. These special characters start with a backslash character. For example, to display the current path in the prompt, use either the `\w` or `\W` character sequence.

```bash
sysadmin@localhost:~/usr/share/doc$
```

Notice that `\w` provides a full path in the prompt (`/usr/share/doc` in the example above) while `\W` provides a relative path in the prompt.

```bash
sysadmin@localhost:~/doc$ pwd
/usr/share/doc
```

### History Variables

Several variables affect the use of the <mark style="color:red;">`history`</mark> command. Recall that the <mark style="color:red;">`history`</mark> command can be used to re-execute previously executed commands. The **`HISTSIZE`** variable will **determine how many commands to keep in memory** for each Bash shell.

When the shell program is close, it **takes commands stored in memory and saves them into the history file, which is `~/.bash_history` by default**. If a user wants to store the history commands in a different file, then the user cna specify an absolute path as the value for the **`HISTFILE`** local variable:

```
HISTFILE=/tmp/history
```

By default, 500 commands will be stored in the history file. The `HISTFILESIZE` variable will determine how many commands to write to this file.

If the values of `HISTSIZE` and `HISTFILESIZE` are different, the lowest of the two numbers is the number of commands that will actually be written to the file.

Although it is not normally set to anything by default, it may be advantageous to set a value for the `HISTCONTROL` variable in an initialization file like the `~/.bash_profile` file. The `HISTCONTROL` variable could be set to any of the following features:

* `ignoredups`: This will prevent duplicate commands that are executed consecutively.
* `ignorespace`: This will not store any command that begins with a space.
* `ignoreboth` :This will not store consecutive duplicates or any command that begins with a space.
* `erasedups`: This will not store a command that is identical to another command in your history.
* `ignorespace:erasedups`: This will include the benefit of `erasedups` with the advantage of `ignorespace`.

Another variable that will affect what gets stored in the history of commands is the `HISTIGNORE` variable. The `HISTIGNORE` variable can be used to tell Bash not to store certain commands in the history list.

To have commands not included in the history list, include a command, like the following, in the `~/.bash_profile` file:

```bash
HISTIGNORE='ls*:cd*:history*:exit'
```

## Aliases

An alias is essentially a nickname for a command or series of commands. Some aliases may be created automatically as the result of commands executed in initializaion shells. To view shell's current aliases, use the <mark style="color:red;">`alias`</mark> command with no arguments:

```bash
sysadmin@localhost:~$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo
 error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'
')"'                                                                            
alias egrep='egrep --color=auto'                                                
alias fgrep='fgrep --color=auto'                                                
alias grep='grep --color=auto'                                                  
alias l='ls -CF'                                                                
alias la='ls -A'                                                                
alias ll='ls -alF'                                                              
alias ls='ls --color=auto'
```

Each line describes an alias in the format that the <mark style="color:red;">`alias`</mark> command is executed when creating new aliases. For example, the alias that defines the _long_ _list_ `ll` command is:

```bash
alias ll='ls -alF'
```

The primary purpose of creating an alias include:

*   **To include a command option by default:**

    For example, if the `-i` option is always used when executing the `rm` command, an alias can be created for `rm`, so it always runs as `rm -i`.&#x20;

```bash
sysadmin@localhost:~$ alias rm='rm -i'
```

*   **To create a short form of a long command:**

    For example, if the command `ls -ld .*` is used often, an alias can be created for this, perhaps called `hidden` ,which display only the hidden files in a directory.

```bash
sysadmin@localhost:~$ alias hidden="ls -ld .*"
sysadmin@localhost:~$ hidden
drwxr-xr-x 1 sysadmin sysadmin 4096 Sep 20 22:49 .                              
drwxr-xr-x 1 root     root     4096 May  2 16:18 ..                             
-rw------- 1 sysadmin sysadmin  194 Sep 20 21:58 .bash_history                  
-rw-r--r-- 1 sysadmin sysadmin  220 Apr  4  2018 .bash_logout                   
-rw-r--r-- 1 sysadmin sysadmin 3768 Apr 24 16:24 .bashrc     
```

*   **To create DOS equivalent commands:**

    Many people need to work both on Microsoft WIndows systems as well as Linux. Subtle differences, such as running `cls` (DOS) instead of `clear` (Linux) to clear the terminal display, can create headaches.

```bash
sysadmin@localhost:~$ cls
-bash: cls: command not found
sysadmin@localhost:~$ alias cls=clear
sysadmin@localhost:~$ cls
```

**Aliases that are created on the command line are specific to the shell that they are created in.** If a new shell is opened, the aliases that are created in this shell will not exist in the new shell.

{% hint style="danger" %}
To make aliases permanent, the <mark style="color:red;">`alias`</mark> command needs to be placed in an initialization shell.
{% endhint %}

To remove an alias from the current shell, use the <mark style="color:red;">`unalias`</mark> command:

```bash
sysadmin@localhost:~$ alias | grep hidden
alias hidden='ls -ld .*'                                                        
sysadmin@localhost:~$ unalias hidden
sysadmin@localhost:~$ alias | grep hidden
sysadmin@localhost:~$ hidden
hidden: command not found
```

To avoid an alias for a single execution of the command, use the backslash `\` character before the command:

```bash
sysadmin@localhost:~$ mkdir test
sysadmin@localhost:~$ ls
Desktop    Downloads  Pictures  Templates  my.sh                                
Documents  Music      Public    Videos     test 
sysadmin@localhost:~$ \rm -r test
sysadmin@localhost:~$ ls
Desktop    Downloads  Pictures  Templates  my.sh                                
Documents  Music      Public    Videos 
```

{% hint style="info" %}
Aliases can also be avoided by using either an absolute or relative path to the command.
{% endhint %}

## Functions

Functions are a bit more advanced than aliases and typically are used in Bash shell scripts.&#x20;

A function is much like an alias in that it provides a nickname for something else that requires execution. However, functions are typically designed to execute multiple commands, not a single command like an alias.

For those familiar with Java, C++, or several other programming language, this will look very familiar. To create a function, use the following syntax:

```
function_name ()
{
    command_here
}
```

When argumens are passed to a function, they are stored in special variables: `$1` for the first argument, `$2` for the second argument, etc.

{% hint style="info" %}
Like aliases, functions only exist in the shell that they are created in. To make tm permanent, place them in an initialization file.
{% endhint %}

### Lists

In the context of the Bash shell, a list is a sequence of commands which are separated by one of the following operators:

| Operators | Meaning                                                                                                                                                                                                                                                                |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `;`       | The commands within the list are executed sequentially, where the shell will execute the first command and wait for it to terminate before executing the next command. The exit status of the list is based upon the exit status of the last command that is executed. |
| `&`       | Each command within the list is executed asynchronously within a subshell, or in the background. The shell does not wait for the commands to terminate and returns an exit status of zero                                                                              |
| `&&`      | This is and AND list, so if the command on the left side of `&&` does execute successfully, then the command on the right side of `&&` will execute.                                                                                                                   |
| `\|\|`    | This is an OR list, so if the command on the left side of `\|\|` does NOT execute successfully, then the command on the right side of `\|\|` is executed.                                                                                                              |

To make sure every command executes in order, use a simple sequential list with the commands separated by the semicolon `;` character. For example:

```bash
sysadmin@localhost:~$ date;who;uptime                                           
Fri Sep 18 23:09:31 UTC 2020                                                    
sysadmin console      Sep 18 21:58                                              
 23:09:31 up 9 days, 21:50,  1 user,  load average: 0.30, 0.48, 0.45
```

Using an OR list, when one command is executed, another command will be executed only if it fails. In this example, the `test` command is used to see if a directory `$HOME/bin` exists. If this test fails, then the directory is created.

```bash
sysadmin@localhost:~$ test -e $HOME/bin || mkdir $HOME/bin
sysadmin@localhost:~$ ls $HOME
Backup   Documents  Music     Public     Videos  my.sh                          
Desktop  Downloads  Pictures  Templates  bin
```

## Initialization Files

When a user opens a new shell, either during login or when they run a terminal that starts a shell, the shell is customized by files called **initialization (or configuration) files**. These initialization files **set the value of variables, create aliases and functions**, and execute other commands that are useful in starting the shell.

There are two types of initialization files: _global_ initialization files that affect all users on the system and _local_ initialization files that are specific to an individual user.

The global configuration files are located in the `/etc` directory. Local configuration files are stored in the user's home directory.

#### BASH Initialization Files

Each shell uses different initialization files. Additionally, most shells execute different initialization files when the shell is started via the login process **(called a login shell)** versus when a shell is started by a terminal (called non-login shell **or an interactive shell).**

The following diagram ilustrates the different files that are started with a typical login shell versus an interactive shell.

![](<../.gitbook/assets/image (21).png>)

When Bash is started as an interactive shell, it executes the `~/.bashrc` file, which may also execute the `/etc/bashrc` file, if exists. Again, since the `~/.bashrc` file is owned by the user who is logging in, the user can prevent execution of the `/etc/bashrc` file.

The following chart illustrates the purpose of each of these files, providing examples of what commands you might place in each file:

| File              | Purpose                                                                                                                                                                                                                                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/etc/profile`    | This file can only be modified by the administrator and will be **executed by every user who logs in**. Administrators use this file to create key environment variables, display messages to users as they log in, and set key system values.                                                    |
| `~/.bash_profile` | Each user has their own `.bash_profile` file in their home directory. The purpose of this file is the same as the `/etc/profile` file, but having this file **allows a user to customize the shell to their own tastes**. This file is typically used to create customized environment variables. |
| `~/.bashrc`       | Each user has their own `.bashrc` file in their home directory. The purpose of this file is to **generate items that need to be created for each shell**, such as local variables and aliases.                                                                                                    |
| `/etc/bashrc`     | This file may **affect every user on the system**. Only the administrator can modify this file. Like the `.bashrc` file, the purpose of this file is to generate items that need to be created for each shell, such as local variables and aliases.                                               |

## Modifying Initialization Files
