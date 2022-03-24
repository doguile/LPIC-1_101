# Chapter 3: Configuring the Shell

<details>

<summary>Key terms</summary>

**`.bash_history`** File used to store the current history list when the shell is closed

**`env`** Print a list of the current environment variables or change to an alternate enviroment

**`export`** Makes an assigned variable available to sub-processes.

**`history`** Print a list of previously executed commands or "re-execute" previously executed commands.

**`set`** Display all variables

**`unset`** Remove one or more variables

</details>

## Shell variables

A _<mark style="color:red;">variable</mark> _ is a identifier that can be assigned a value. You assign a value to a variable by typing the name of the variable immediately followed by the equal sign = character and then the value.

```
name="Bob Smith"
```

**Variable names should start with a letter(alpha character) or underscore and contain only letters, numbers and the underscore character**. The variable names are case sensitive; `a` and `A` are different variables.

For the shell, <mark style="background-color:red;"></mark> <mark style="background-color:red;"></mark><mark style="background-color:red;">**variables can affect what the prompt displays**</mark>, the directories the shell will search for commands to execute and much more.

## Local and Environment Variables

A <mark style="color:red;"></mark> <mark style="color:red;"></mark>_<mark style="color:red;">local variable</mark> _ is **only available to the shell in which it was created**. An _<mark style="color:red;">environment variable</mark>_ is **available to the shell in which it was created**, and it is passed into all other commands/programs started by the shell.

If the variable already exists, the value of the variable is modified. If the variable does not exist, the shell creates a new local variable and sets the value.

{% hint style="warning" %}
Lowercase characters are used to create local variables names, and Uppercase characters are used when naming an environment variable.&#x20;

PD: This is not a rule
{% endhint %}

An _<mark style="color:red;">environment variable</mark> _ can be created directly by using the <mark style="color:red;">**`export`**</mark> command.

```bash
export BOB=engineer
```

To display the value of the variable, use the <mark style="color:red;">**`$`**</mark> character followed by the variable name as an argument to the <mark style="color:red;">**`echo`**</mark> command.

```bash
echo $BOB
```

The _<mark style="color:red;">local variables</mark>_ are not available in new shells because by default, **when a variable is assigned in the Bash shell, it is initially set as a local variable**. When you exit the original shell, only the environment variable will be available.

_Local variable_ can be exported with the <mark style="color:red;">**`export`**</mark> command.

```
export variable
```

```
NAME=pedro
export NAME
```

The <mark style="color:red;">**`declare`**</mark> and <mark style="color:red;">**`typeset`**</mark> command can be used with the export <mark style="color:red;">**`-x`**</mark> option to **declare a variable to be an environment variable**. These commands are synonymous and work the same way.

```
declare -x PERSON=Juan
typeset -x LASTNAME=Gomez
```

The <mark style="color:red;">**`env`**</mark> command is used to **run commands in a modified environment**. It can also be used to temporarily create or change environment variables that are only passed to a single command execution.

```
env NAME=value command
env TZ=EST date
```

> The TZ is set only in the environment of the current shell, and only for the duration of the command. The rest of the system will no be affected by this variable.

### Displaying Variables

The <mark style="color:red;">`set`</mark> command by itself **will display all variable** <mark style="background-color:red;">**(local and environment)**</mark>.

```bash
set | less
```

To display **only** _**environment variables**_, you can use several commands that provide nearly the same output:

```bash
env
declare -x
typeset -x
export -x
env | tail
```

To display the value of a specific variable, use the <mark style="color:red;">`echo`</mark> command with a name of the variable prefixed by the <mark style="color:red;">**`E`**</mark>

### Unsetting Variables

You can delete variables with the <mark style="color:red;">`unset`</mark> command.

```bash
unset variable
```

### PATH Variable

The PATH variable contains a list of directories that are used to search for commands entered by the user. Processing works through the list of directories from the left to right.

{% hint style="info" %}
&#x20;If the command happens to be built-in to the shell, the `PATH` variable will not be utilized.
{% endhint %}

```bash
echo $PATH
/home/sysadmin/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

The following table illustrates the purpose of some of the directories displayed in the output of the PATH

| Directory         | Contents                                                                                                              |
| ----------------- | --------------------------------------------------------------------------------------------------------------------- |
| `/home/user1/bin` | A directory for the current user `user1` to place programs. **Typically used by users who create their own scripts.** |
| `/usr/local/sbin` | Normally empty, but may have **administrative commands** that have been **compiled from local sources**               |
| `/usr/local/bin`  | Normally empty, but may have **commands** that have been **compiled from local sources**.                             |
| `/usr/sbin`       | Contains the majority of the **administrative command files.**                                                        |
| `/usr/bin`        | Contains the majority of the **commands that are available for regular users to execute**.                            |
| `/sbin`           | Contains the **essential administrative commands**                                                                    |
| `/bin`            | Contains the most **fundamental commands** that are essential **for the operating system to function.**               |

Sometimes a user wants their home directory added to the PATH variable in order to run scripts and programs without using `./` in front of the file name. They might be tempted to modify the PATH variable like so:

```
PATH=/home/user1
```

Unfortunately, modifying a variable this way overwrites the contents. Therefore, everything that was previously contained in the `PATH` variable will be lost.

It is possible to add a new directory to the <mark style="color:red;">`PATH`</mark> variable without overwriting its previous contents. Import the current value of the <mark style="color:red;">`$PATH`</mark> variable into a newly defined PATH variable by using it on both side if the assignment statement. Finish it with the value of the additional home directory path:

```
PATH=$PATH:/home/user1
```

Now scripts located in /home/user1 directory can execute without using a path or `./`

## Initialization Files

When a user opens a new shell, either during login or when they run a terminal that starts a shell, the shell is customized by files called _initialization_ (or configuration files). These initialization files set the values of variables, create aliases and functions, and execute other commands useful for the shell.

There are two types of _initialization files_: **global initialization files** that affect all users on the system and **local initialization files** that are specific to an individual user.

The **global configuration files** are located in the `/etc` directory. **Local configuration files** are stored in the user's home directory.

### BASH Initialization Files

Most shells execute different initialization files when the shell is started via the login process (called a _login shell_) vs when a shell is started by a terminal (called a non-login shell or _interactive shell_).

#### Bash started as a login shell

1. When Bash is started as a login shell, the `/etc/profile` file is executed first. This file execute all files ending in .sh that are found in the `/etc/profile.d` directory.
2. The next file that is executed is usually the `~/.bash_profile` (or `~/.bash_login` or `~/.profile` file). The `./bash_profile` file also executes the `~/.bashrc` file which in turn executes the `/etc/bashrc` file

BASH started as a interactive shell



1. Executes the `~/.bashrc` file, which may also execute the `/etc/bashrc` file, if it exists. Again, since the `~/.bashrc` is owned by the user who is logging in, the user can prevent execution of the `/etc/bashrc` file.

|                                                 File                                                 | Purpose                                                                                                                                                                                                                                                                                                                                                |
| :--------------------------------------------------------------------------------------------------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|                                            `/etc/profile`                                            | **Is for all bash shell users and executed only at login**. This file can only be modified by the administrator and will be **executed by every user who logs in**. Administrators use this file to create key environment variables, display messages to users as they log in, and set key system values.                                             |
| <p><code>~/.bash_profile</code> </p><p><code>~/.bash_login</code></p><p> <code>~/.profile</code></p> | Each user has their own `.bash_profile` file in their home directory. The purpose of this file is the same as the `/etc/profile` file, but having this file allows a user to customize the shell to their own tastes. This file is typically used to create customized environment variables. **specific to each user and executed only during login** |
|                                              `~/.bashrc`                                             | **Each user has their own `.bashrc` file** in their home directory **and executed every time a bash shell is opened.** The purpose of this file is to generate items that need to be created for each shell, such as local variables and aliases.                                                                                                      |
|                                             `/etc/bashrc`                                            | **Is for all bash shell users and executed every time a bash shell is opened**. This file may affect every user on the system. Only the administrator can modify this file. Like the `.bashrc` file, the purpose of this file is to generate items that need to be created for each shell, such as local variables and aliases.                        |

## Command History

In a sense, the `~/.bash_history` file could also be considered to be an initialization file, since Bash also reads this file as it starts up. By default, this file contains a history of the commands that a user has executed within the Bash shell. When a user exits the Bash shell, it writes out the recent history to this file.

### Changing Editing Keys

The keys that are available for editing a command are determined by the settings of a library called **Readline.** The keys are typically set to match the key assignments found within the <mark style="color:red;">`emacs`</mark> text editor.

To bind the keys to match the key assignments found with another popular text editor, the `vi` editor, the shell can be configured with the `set -o vi` command. To set the key bindings back to the `emacs` text editor, use the `set -o emacs` command.

To automatically configure the edit history options at login, edit the `~/.inputrc` file. If this file doesn't exist, the `/etc/inputrc` file is used instead. Key bindings are set differently in configuration files than on the command line; for example, to enable the `vi` key binding mode for an individual, add the following lines to the `~/.inputrc` file:

```
set editing-mode vi
set keymap vi
-------------------
set editing-mode emacs
set keymap emacs
```

### Using the history command

The <mark style="color:red;">`history`</mark> command can be used to re-execute previously executed commands.

The `history` command has numerous options; the most common of these options are listed below:

| Option                                                                                                 | Purpose                                               |
| ------------------------------------------------------------------------------------------------------ | ----------------------------------------------------- |
| <mark style="color:red;">`-c`</mark>                                                                   | Clear the list                                        |
| <mark style="color:red;">`-r`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark>  | Read the history file and replace the current history |
| <mark style="color:red;">`-w`</mark>                                                                   | Write the current history list to the history file    |

### Configuring the history command

When you close the shell program, it takes commands in the history list and stores them in the `~/.bash_history` file, also called the _history file_. By default, **500 hundred commands will be stored** in the history file. **The `HISTFILESIZE` variable will determine how many commands to write to this file.**

If a user wants to store the history commands in a file different from the `~/.bash_history`, then the **user can specify an absolute path to the different file** as the value for the `HISTFILE` local variable.

```
HISTFILE=/path/to/file
```

The `HISTSIZE` variable will determine **how many commands to keep in memory** for each Bash shell. If the size of `HISTSIZE` is greater than the size of `HISTFILESIZE`, then only the most recent number of commands specified by HISTFILESIZE will be written to the history file when the Bash shell exists.

Although it is not normally set to anything by default, you may want to take advantage of setting a value for the `HISTCONTROL` variable in an initialization file like the `~/.bashrc` file. The `HISTCONTROL` variable could be set to any of the following features:

```bash
#Prevents duplicate commands that are executed consecutivelyb
HISTCONTROL=ignoredups

#Any command that begins wiht a space will not be stored.
HISTCONTROL=ignorespace

#Duplicates and any commands that begin with a space will not be stored
HISTCONTROL=ignoreboth

#Commands that are identical to another command in your history will not be stored
HISTCONTROL=erasedups

#Inclues the benefit of erasedups with the advantage of ignorespace
HISTCONTROL=ignorespace:erasedups
```

Another variable that will affect what gets stored in the history of commands is the `HISTIGNORE` variable. The `HISTIGNORE` variable can be used to tell Bash not to store certain commands in the history list.

To have commands not included in the history list, include an assignment in the `./bashrc`

```bash
HISTIGNORE='ls*:cd*:history*:exit'
```

### Executing previous commands

The `!` exclamation mark is a special character to the Bash shell to indicate the execution of a command within the history list. There are many ways to use the `!` exclamation character to re-execute commands; for example, executing two exclamation characters will repeat the previous command.

| History Command                         | Meaning                                            |
| --------------------------------------- | -------------------------------------------------- |
| <mark style="color:red;">`!!`</mark>    | Repeat the last command                            |
| <mark style="color:red;">`!-4`</mark>   | Execute the command that was run four commands ago |
| <mark style="color:red;">`!555`</mark>  | Execute command number `555`                       |
| <mark style="color:red;">`!ec`</mark>   | Execute the last command that started with `ec`    |
| <mark style="color:red;">`!?joe`</mark> | Execute the last command that contained `joe`      |

