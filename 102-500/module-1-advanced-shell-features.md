# Module 1: Advanced Shell Features

## Chapter 1: Advanced Shell Features

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

Second, a new variable can be exported and
