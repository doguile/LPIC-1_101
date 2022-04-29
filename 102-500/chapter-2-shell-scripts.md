---
description: '105.2: Customize or write simple scripts v2  Weight: 4'
---

# Chapter 2: Shell scripts

<details>

<summary>Key terms</summary>

`exec` Replaces the current process with a specified command. If no command is specified, the `exec` command spawns a new shell process and returns a shell prompt

`if` if \<condition1> is met, do \<command1>; otherwise, if condition2 is med, do \<command2>; if neither is met, do \<command3>

`read` Reads one line of standard input and assigns each word to the corresponding variable, with all leftover words assigned to the last variable.

`seq` Prints a sequence of numbers from FIRST to LAST, in steps of INCREMENT. Generally, used in shell script

`test` Used with conditional constructs in shell scripts; test evaluates a condition and, if its value is true, returns a zero exit status; otherwise, returns a nonzero exit status

`while` while \<condition1> is met, do \<command1>.

</details>

## Introduction

When you find yourself typing the same sets of commands on regular basis, it is time to consider creating a shell script. At the most simple level, a shell script is just a set of commands placed into a text file, which, when invoked as a program, will execute each individual command in order.

Shell scripting can be quite complex and utilize powerful features. In addition to all of the features that the interactive shell provides (i.e., variables, aliases, functions), administrators can also make use of programming features such as if statements, while loops and for loops, to name a few. While not considered by some as robust as more advanced programming languages, shell scripts can be very powerful because they make direct use of shell commands.

## Shell Scripting Basics

To create a shell script, use the following steps:

* Use a text editor to create a file with the commands that you want to execute
* Place the following line at the beginning of the file to indicate that the script is to be run in the Bash shell.

```bash
#!/bin/bash
```

* Use the `chmod` command to make the file executable

In the following example, a scritp has been created that will create a report of disk space usage in the `/usr/share/doc` directory.

```bash
sysadmin@localhost:~$ more report.sh                                       
#!/bin/bash                                                                     
cd /usr/share/doc                                                               
echo "Document directory usage report" > /tmp/report                            
date >> /tmp/report                                                             
pwd >> /tmp/report                                                              
du -sh . >> /tmp/report   
                                                      
sysadmin@localhost:~$ chmod a+x report.sh                                  
sysadmin@localhost:~$ ./report.sh                                          
sysadmin@localhost:~$ more /tmp/report                                     
Document directory usage report                                                 
Mon Jun 15 06:41:17 UTC 2015                                                    
/usr/share/doc                                                                  
6.6M    .
```

{% hint style="info" %}
Typically a pound sign <mark style="color:red;">`#`</mark> character is used to comment a line, but when placed at the beginning of the first line and followed by an exclamation mark `!` character, it is used to tell the system which executable to use to run the program.
{% endhint %}

## Script Placement

Consider placing the script in a location that is specified by the `PATH` variable. A typical `PATH` variable will include the following directories:

```bash
sysadmin@localhost:~$ echo $PATH                                                
/home/sysadmin/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:
/usr/games:/usr/local/games
```

With administration rights, a script can be placed in any of the directories listed in the `PATH` variable. However, **the ideal location for scripts designed to be shared by regular users would be the **<mark style="color:red;">**`/usr/local/bin`**</mark>** directory**. For scripts designed to be executed by administrators place the script in the `/usr/local/sbin` directory.

Regular users don’t have the rights to place files in these directories. **For a user’s own script, the ideal place to put the script is the **<mark style="color:red;">**`/home/USER/bin`**</mark>** directory**, where `USER` is the name of the current user account.&#x20;

## Script Permissions

Typically, a script will need to have both read and execute permissions for the user who is attempting to execute it.&#x20;

If the script is designed for private use, consider only providing the execute permission for the user: `chmod u+x file_name`

If the script must be executed with root privileges by an ordinary user, then a simple _wrapper_ around the script can be created in a language that compiles to binary, and the binary executable can be _setuid._

**A wrapper is a script whose purpose is to simply configure and launch another program**. One way to creae a wrapper script is to use the <mark style="color:red;">`exec`</mark> command, which takes another command to execute as an argument. A wrapper script often uses the following as the last line of the script to execute another program:

```
exec program
```

The <mark style="color:red;">`exec`</mark> command replaces the current process with a specified command. If no command is specified, the <mark style="color:red;">`exec`</mark> command spawns a new shell process and returns a shell prompt. Running the <mark style="color:red;">`exec`</mark> command with no argument allows a user to change the current shell environment, including using redirection.

```bash
sysadmin@localhost:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos    
sysadmin@localhost:~$ exec > my_commands.txt
sysadmin@localhost:~$ ls
```

Notice in the example above, there was no output for the `ls` command because the <mark style="color:red;">`exec`</mark> command has replaced the current process and is redirecting all output to the `my_commands.txt` file. Exit the `exec` session and then open a new shell:

```bash
sysadmin@localhost:~$  exit 
sysadmin@localhost:~$ ls my_commands.txt
my_commands.txt
sysadmin@localhost:~$ cat my_commands.txt                                      
Desktop                                                                         
Documents                                                                       
Downloads                                                                       
Music         
```

The <mark style="color:red;">**`exec`**</mark> command is also **commonly used in scripts to start a new shell with a clean environment** as read by the shell startup files.

## Command Substitution

A shell feature that is very useful when creating scripts is called _command substitution_. This allows the script to execute a shell command and r**edirect the output of the command into a variable** instead of having the output displayed on the terminal. For example:

```bash
sysadmin@localhost:~$ start=`date`
```

The backqoute characters around <mark style="color:red;">`date`</mark> tell the shell to execute the command and replace date with the output of the `date` command. This output is then assigned to the start variable:

```bash
sysadmin@localhost:~$ echo $start                                       
Fri Sep 18 03:58:34 UTC 2020
```

The backquote character method of running a subcommand has its origins in the original Bourne Shell. **In the Bash shell, you can also use the syntax of **<mark style="color:red;">**`$(command)`**</mark>:

```bash
sysadmin@localhost:~$ end=$(date)        
sysadmin@localhost:~$ echo $start $end                                          
Fri Sep 18 03:58:34 UTC 2020 Fri Sep 18 03:59:22 UTC 2020
```

> Both techniques accomplish the same task.

Command substitution can also be used to insert the output of a command as another command's argument. For example:

```bash
sysadmin@localhost:~$ echo "Today is $(date)"                                   
Today is Fri Sep 18 04:01:39 UTC 2020
```

This can be very useful when you have a file that contains data like user names, directory names, or file names.&#x20;

```bash
sysadmin@localhost:~/test$ more dirs.txt                                        
/usr/share/doc                                                                  
/usr/local                                                                      
/etc/magic                                                                      
sysadmin@localhost:~/test$ du -sh $(cat dirs.txt)                               
6.6M    /usr/share/doc                                                          
4.0K    /usr/local                                                              
‌⁠​​⁠​ 4.0K    /etc/magic
```

## `read` Statement

The <mark style="color:red;">`read`</mark> statemnet **can be used to gather information from the user** who is running the script. The user will be presented with a blinking cursor that will **accept keyboard input and place what the user types** into oner or more variables.

```
read variable
```

For example, the following command will read the user's input into a variable called `name:`

```bash
read name
```

It would be helpful for the user to know what sort of input is being requested. The <mark style="color:red;">`-p`</mark> option to the <mark style="color:red;">`read`</mark> command allows you to issue a prompt:

```bash
read –p "Please enter your name: " name
```

In the following example script, user input is used to determine which directory name to use to execute the `du` command:

```bash
#!/bin/bash

read -p "Enter a directory : " dir
start=$(date)
echo "Document directory usage report" > /tmp/report
du -sh $dir >> /tmp/report
echo "Start of report: $start" >> /tmp/report
echo "End of report: $(date)" >> /tmp/report
```

```bash
sysadmin@localhost:~/test$ ./report3.sh                                         
Enter a directory: /etc/magic                                                   
sysadmin@localhost:~/test$ more /tmp/report                                     
Document directory usage report                                                 
4.0K    /etc/magic                                                              
Start of report: Mon Jun 15 07:03:56 UTC 2015                                   
End of report: Mon Jun 15 07:03:56 UTC 2015
```

## `test` Statement

While writing shell scripts, there will be times when an administrator will want to execute some commands based on if a _conditional statement_ is true or false. A conditional statement can be used to determine the following:

* If two strings variables or values match ( or don't match)
* If two numeric variables or values match ( or don't match)
* If a command completes successfully
* The status of file (if file exists, if file is a directory, etc)

Typically, programmers will use the <mark style="color:red;">`test`</mark> command as a conditional statement. The <mark style="color:red;">`test`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> comman can perform numeric comparisions, string comparisions, and file testing operations.

The following chart illustrates some of the comparisions that are possible:

| Type                                                             | Symbol | Example              |
| ---------------------------------------------------------------- | ------ | -------------------- |
| True if length of string is zero                                 | `-z`   | `–z string`          |
| True if length of string is not zero                             | `-n`   | `–n string`          |
| True if strings are equal                                        | `=`    | `string1 = string2`  |
| True if strings are not equal                                    | `!=`   | `string1 != string2` |
| True if integers are equal                                       | `-eq`  | `int1 -eq int2`      |
| True if integers are not equal                                   | `-ne`  | `int1 -ne int2`      |
| True if first integer is greater than second integer             | `-gt`  | `int1 -gt int2`      |
| True if first integer is greater than or equal to second integer | `-ge`  | `int1 -ge int2`      |
| True if first integer is less than second integer                | `-lt`  | `int1 -lt int2`      |
| True if first integer is less than or equal to second integer    | `-le`  | `int1 -le int2`      |
| True if file is a directory                                      | `-d`   | `-d file`            |
| True if file is a plain file                                     | `-f`   | `-f file`            |
| True if file exists                                              | `-e`   | `-e file`            |
| True if file has read permission for current user                | `-r`   | `-r file`            |
| True if file has write permission for current user               | `-w`   | `-w file`            |
| True if file has execute permission for current user             | `-x`   | `-x file`            |

While you can execute the <mark style="color:red;">`test`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> command specifically, within an `if` statement, you can have the test statement implicity called by placing the arguments of the test statement within square brackets. So, instead of writing...

```bash
if test $var –eq 7
```

...you could write:

```bash
if [ $var –eq 7 ]
```

{% hint style="danger" %}
When using the square bracket technique, **make sure you place space characters in front of and after the brackets**. If you don’t, an error will occur.
{% endhint %}

## `if` Statement

A script performs different functions based on test, called _branching_. The `if` statement is the basic operator to implement branching.

The basic syntax of an `if` statement is:

```bash
if COND
then
    TRUE_COMMAND(S)
fi
```

Replace `COND` with a conditional expression, like a test statement. Replace `TRUE_COMMAND(S)` with the commands you want to execute if the `COND` statement returns true. The word `if` spelled backward, `fi`, ends the `if` statement.

In the example above, if the `COND` statement returns false, then no commands would be executed. To have commands executed if the `COND` statement returns false, include an `else` clause:

```bash
if COND
then
    TRUE_COMMAND(S)
else
    FALSE_COMMAND(S)
fi
```

If the conditional statement returns false, all of the commands after `else` and before `fi` will be executed.

You might also want to perform a secondary check; this can be accomplished with an `else if` statement, which in Bash is denoted with the keyword `elif`:

```bash
if COND1
then
    TRUE_COMMAND
elif COND2
then
    TRUE2_COMMAND
else
    FALSE_COMMAND
fi
```

If the first condition `COND1` is true, then the `TRUE1_COMMAND(S)` would be executed. If the first condition is false and the second condition `COND2` is true, then the `TRUE2_COMMAND(S)` would be executed. If neither condition is true, then the `FALSE_COMMAND(S)` would be executed.

### Examples of the `if` Statement

The following examples are provided on the command line for visual demonstration purposes. Typically, you would see these statements in a Bash shell script, as will be demonstrated later.

In the first example, a conditional check is performed on the `$USER` variable that contains the current user account name:

```bash
sysadmin@localhost:~$ echo $USER                                                
sysadmin                                                                        
sysadmin@localhost:~$ if [ $USER = 'sysadmin' ]                                 
>                     then                                                      
>                         echo 'hi sysadmin!'                                   
>                     fi                                                        
hi sysadmin!
```

Notice what happens in the next example when a string is used in a numeric comparison. An error like the following would cause a Bash shell script to exit prematurely:

```bash
sysadmin@localhost:~$ echo $USER                                                
sysadmin                                                                        
sysadmin@localhost:~$ if [ $USER -eq 'sysadmin' ]                               
>                     then                                                      
>                         echo 'hi sysadmin!'                                   
>                     fi                                                        
-bash: [: sysadmin: integer expression expected
```

In the next example, a conditional check is performed to determine if the `/etc/passwd` file is readable for the current user:

```bash
sysadmin@localhost:~$ ls -l /etc/passwd                                         
-rw-r--r-- 1 root root 1602 May  2 16:18 /etc/passwd                             
sysadmin@localhost:~$ if [ -r /etc/passwd ]                                     
>                     then                                                      
>                         echo "/etc/passwd is readable"                        
>                     fi                                                        
/etc/passwd is readable
```

The final two examples below both perform the same function, to indicate the type of operating system. The first uses two isolated `if` statements, while the second uses an `else` statement, nesting an `if` statement inside:

```bash
#!/bin/bash                                                                     
ARCHITECTURE=`uname -m`                                                         
if [ $ARCHITECTURE = "i686" ]                                                   
then                                                                            
        echo "32 Bit Operating System Detected"                                 
else                                                                            
        if [ $ARCHITECTURE = "x86_64" ]                                         
        then                                                                    
                echo "64 Bit Operating System Detected"                         
        fi                                                                      
fi
```

## Test Return Values

**Every command has a return or exit status value that can be used to determine if the command succeeded or failed**. This return value is a number that can only be an integer value from 0 to 255. A value of 0 means the command executed successfully while a positive integer value means the command failed.

To see the exit status of a command, view the `$?` variable:

```bash
sysadmin@localhost:~$ grep sysadmin /etc/passwd                                 
sysadmin:x:1001:1001:System Administrator,,,,:/home/sysadmin:/bin/bash           
sysadmin@localhost:~$ echo $?                                                   
0                                                                               
sysadmin@localhost:~$ grep sysadmin /etc/shadow                                 
grep: /etc/shadow: Permission denied                                            
sysadmin@localhost:~$ echo $?                                                   
2
```

In the previous example, the first `grep` command executed successfully, resulting in a value of `0` in the `$?` variable. The second `grep` command failed, resulting in a value of `2` in the `$?` variable.

Sometimes these different values are documented in the `EXIT STATUS` section of the command's man page.

