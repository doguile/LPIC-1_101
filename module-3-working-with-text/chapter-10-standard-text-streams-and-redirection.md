# Chapter 10: Standard text streams and Redirection

In order to be able to apply filter commands and work with text streams, it is helpful to understand a few forms of _redirection_ that can be used with most commands: **pipelines**, **standard output redirection**, **error output redirection**, and **input redirection**.

## Standard Output

When a command executes without any errors, the output that is produced is known as _standard out,_ also called <mark style="color:red;">`stdout`</mark> or <mark style="color:red;">`STDOUT`</mark> .By default, this output will be sent to the terminal where the command is being executed.

Standard output redirection is achieved by following a command with the greather-than `>` character and a destination file.

```bash
ls -l > home.txt
```

Redirecting the output using a single greather-than `>` character will create a new file, or overwrite the contents of an existing file with the same name. Redirecting standard output with two greather-than `>>` characters will also create a new file if it does not exist. **The difference is that when using the `>>` characters , the output of the command will be appended to the end of the file if it does already exist.**

There is a number associated with the standard output file descriptor (the `>` character): **the number 1 (one)**. However, since standard output is the most commonly redirected command output , _the number can be omitted._

| <pre><code>COMMAND > FILE</code></pre><pre><code>COMMAND 1> FILE</code></pre>   | Create or overwrite `FILE` with the standard output of `COMMAND` |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| <pre><code>COMMAND >> FILE</code></pre><pre><code>COMMAND 1>> FILE</code></pre> | Create or append to `FILE` with the standard output of `COMMAND` |

## Standard Error

When a command encounters an error, it will produce output that is know as _**standard error**,_ also called <mark style="color:red;">`stderr`</mark> or <mark style="color:red;">`STDERR`</mark> .Like standard out, the standard error output is normally sent to the same terminal where the command is currently being executed. The number associated with the **standard error file descriptor is 2** (two).

To redirect these error messages, you must use the correct file descriptor, which for standard error is the number 2. For example:

```bash
ls -l /junk 2> prueba.txt
```

To prevent clobbering an existing file when redirecting standard error, use the double `>>` characters after the number 2 to append instead:

```bash
ls /junk 2>> prueba.txt
```

Some commands will produce both `stdout` and `stderr` output. These two differents outputs can be redirected into two separate files by using the following syntax:

```bash
find /etc -name passwd > output.txt 2> error.txt
```

{% hint style="warning" %}
Sometimes it isn't useful to have the error messages displayed in the terminal or stored in file. To discard these error messages, **use the `/dev/null` file.**
{% endhint %}

The `/dev/null` file is like a trash can, where anything sent to it disappears from the system; it'sometimes called the bit bucket or black hole. Any type of output can be redirected to the `/dev/null`

```bash
find /etc -name passwd 2> /dev/null
```

There are two techniques to redirect the both standard error and standard output to the same file, and if you want to appended just use the `>>` character:

```bash
ls -l /etc > /tmp/ls.all 2>&1
ls -l /etc &> /tmp/ls.all

ls /etc/au* >> /tmp/ls.all 2>&1
ls /etc/au* &>> /tmp/ls.all
```

## Standard Input

_Standard in_, also called `stdin` or `STDIN` ,normally comes from the keyboard with input provided by the user who runs the command.&#x20;

{% hint style="info" %}
One common way that text files are used as standard input for commands is by creating script files. Scripts are text files which are interpreted by the shell, prefaced with <mark style="color:red;">`#!/bin/sh`</mark> on the first line, which tells the shell to interpret the script as standard input

When the script is invoked at the prompt using the <mark style="color:orange;">`./`</mark> syntax, the shell will run all commands in the script file and return to the terminal window.
{% endhint %}

In some cases, it is useful to redirect standard input, so it comes from a file instead of the keyboard . A good example of when input redirection is desirable involves the <mark style="color:red;">`tr`</mark> command.&#x20;

The <mark style="color:red;">`tr`</mark> command won't accept a file name as an argument on the command line. To perform a translation using a file as input, utilize input redirection. To use input redirection, type the command with its options and arguments followed by the less-than <mark style="color:red;">`<`</mark> character and a path to a file to use for input. For example:

```bash
sysadmin@localhost:~$ cat Documents/animals.txt
1 retriever
2 badger
3 bat
4 wolf
5 eagle
sysadmin@localhost:~$ tr 'a-z' 'A-Z' < Documents/animals.txt
1 RETRIEVER
2 BADGER
3 BAT
4 WOLF
5 EAGLE
sysadmin@localhost:~$ cat Documents/animals.txt | tr 'a-z' 'A-Z' > animales.txt
```

## Command Pipelines

Command pipelines are often **used to make effective use of filter commands**. In a command pipeline, **the output of one commands is sent to another command as input.** In linux and most operating system, the vertical bar or pipe `|` character is used between two commands to represent a command pipeline.

Imagine that the output of the  `history` command is very large. To send this output to the `less` command, which displays one "page" of data at the time, the following pipeline will be used:

```bash
history | less
```

Command pipelines becomes really powerful when three or more commands are combined. For example, the following command will extract some fields from the os.csv file with the <mark style="color:red;">`cut`</mark> command, the sort these lines with the <mark style="color:red;">`sort`</mark> command, and finally eliminate duplicates lines with the <mark style="color:red;">`uniq`</mark> command:

```bash
sysadmin@localhost:~$ cat Documents/os.csv
1970,Unix,Richie
1987,Minix,Tanenbaum
1970,Unix,Thompson
1991,Linux,Torvalds

sysadmin@localhost:~$ cut -f1 -d',' os.csv | sort -n | uniq 
1970
1987
1991
```

## tee Command

The <mark style="color:red;">`tee`</mark> command splits the output of a command into two streams: one directed to standard output, which displays in the terminal, and the other into a file.

{% hint style="info" %}
The `tee` command read from standard input and write to standard output and files
{% endhint %}

The <mark style="color:red;">`tee`</mark> command can be very useful to create a log of a command or a script.&#x20;

```bash
sysadmin@localhost:~$ date | tee timer.txt
Fri Nov  7 02:21:24 UTC 2022
```

The `timer.txt` file now contains a copy of the date, the same output displayed in the preceding example:

```bash
sysadmin@localhost:~$ cat timer.txt
Fri Nov  7 02:21:24 UTC 2022
```

To append the time to the end of the `timer.txt` file by using the <mark style="color:red;">`-a`</mark> option:

```bash
sysadmin@localhost:~$ date | tee -a timer.txt
Fri Nov  7 02:28:43 UTC 2022
```

Both times will then be recorded in the file.

To run all of the above commands as a single command, use a semicolon <mark style="color:red;">`;`</mark> character as a separator:

```bash
sysadmin@localhost:~$ date | tee timer.txt; sleep 15; date | tee -a timer.txt
Fri Nov  7 02:35:47 UTC 2022
Fri Nov  7 02:36:02 UTC 2022
```

## `xargs` Command

We can use the <mark style="color:red;">`xargs`</mark> command to gather arguments from another input source (such as file or standard input) and the pass those arguments to a command. The <mark style="color:red;">`xargs`</mark>command can be called directly and will accept any input.

The default action of the `xargs` command is to pass the input to the `echo` command when another command does not explicitly follow it.

**The **<mark style="color:red;">**`xargs`**</mark>** command is most useful when its called in a pipe.** In the next example, four files will be created using the `touch` command.

```bash
sysadmin@localhost:~$$ echo '1a 1b 1c 1d' | xargs touch
sysadmin@localhost:~$ ls
1a  1c  Desktop    Downloads  Pictures  Templates  timer.txt
1b  1d  Documents  Music      Public    Videos
```

A delimiter can be set using the <mark style="color:red;">`-d`</mark> option with <mark style="color:red;">`xargs`</mark> command. To view the contents of the /Document directory containing the word alpha with all instances of the dash `-` character replaced with a space, type the following:

```bash
sysadmin@localhost:~/Documents$ ls | grep alpha | xargs -d '-'
alpha first.txt
alpha second.txt
alpha third.txt
alpha.txt
```

After running the `xargs` command with the `touch` command, eleven new files appear:

```bash
sysadmin@localhost:~/Documents$ cat red.txt | xargs touch
sysadmin@localhost:~/Documents$ ls
School            alpha.txt    linux.txt     profile.txt  reeed  rot
Work              animals.txt  longfile.txt  rd           reef   spelling.txt
adjectives.txt    food.txt     newhome.txt   read         reel   words
alpha-first.txt   hello.sh     numbers.txt   red          rod
alpha-second.txt  hidden.txt   os.csv        red.txt      roof
alpha-third.txt   letters.txt  people.csv    reed         root

```

In the previous example, the output of the <mark style="color:red;">`cat`</mark> command was used as input to the <mark style="color:red;">`touch`</mark> command using <mark style="color:red;">`xargs`</mark> and a command-line pipe.

The xargs command can also be used with pipes to send the output of a command as an argument to another command:

```bash
sysadmin@localhost:~$ find -maxdepth 1 -name 'D*' | xargs du -sh
4.0K    /home/sysadmin/Downloads
1.2M    /home/sysadmin/Documents
4.0K    /home/sysadmin/Desktop
```

In the example above, the `find` command output is used as an argument to the `du` disk usage command using the `xargs` command. The first command preceding the pipe finds all file names that match the `D*` pattern. The output of the `find` command is then passed to the `du` command as an argument to display the disk usage of the files.



















\
\
