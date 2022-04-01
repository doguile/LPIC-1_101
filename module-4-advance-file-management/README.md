---
description: >-
  103.5: Create, monitor and kill processes v2  Weight:
  4                                                                                        
  103.6: Modify process execution priorities v2 Weight: 2
---

# Module 4: Advance File Management

<details>

<summary>Key terms</summary>

<mark style="color:red;">`&`</mark> When used with a process will immediately background the process or task in a terminal. Allow the terminal to be freed up for additional tasks.

<mark style="color:red;">`bg`</mark> A job control command that resumes the execution of a suspended process without bringing it to the foreground.

<mark style="color:red;">`fg`</mark> A job control command that resumes execution of a suspended process by bringing it to the foreground.

<mark style="color:red;">`free`</mark> Command used to display the amount of free and used memory in the system. This utility will display both the physical and swap memory on the system as well as the buffers used by the kernel.

<mark style="color:red;">`jobs`</mark> Command that lists all active jobs in the current terminal or shell

<mark style="color:red;">`kill`</mark> Command used to terminate a process.

<mark style="color:red;">`killall`</mark> Command similar to `kill` ,but `killall` will allow a user to terminate a process by name as oppesed to ID

<mark style="color:red;">`nice`</mark> Command used to change the priority of a process. The higher the nice value the lower priority of a process. Nice values range from -20 to 19

<mark style="color:red;">`nohup`</mark> Command used to run a command immune to hangups, with output to a non-tty.

<mark style="color:red;">`pgrep`</mark> Command that parses through the running processes and looks for the specified names or other criteria of processes.

<mark style="color:red;">`pkill`</mark> Sends a specified signal (SIGTERM by default) to each process instead of listing them on `stdout`

<mark style="color:red;">`ps`</mark> Command used to report a snapshot of the current processes

<mark style="color:red;">`renice`</mark> Command used to change the priority of a process while that process is running.

<mark style="color:red;">`screen`</mark> A full-screen software program that can be used to multiplex a physical console between several processes.

<mark style="color:red;">`tmux`</mark>

<mark style="color:red;">`top`</mark> Command similar to `ps` ,however top will display the current real-time view of all running tasks and processes.

<mark style="color:red;">`uptime`</mark>Command that will display how long a system has been running, how many users are logged in, and the system load averages for the pas 1,5, and 15 minutes.

<mark style="color:red;">`watch`</mark>

</details>

## Chapert 11: Managing Processes

## Process Control

Running a command results something called a _process_. In the Linux operting system, processes are executed with the privileges of the user who executes the command.&#x20;

Generally the operating system will differentiate users based upon whether they are administrator (also called the root user) or not. Users who have logged into the root account can control any users processes, including stopping any user process.

### Listing Proccesses

The <mark style="color:red;">`ps`</mark> command can be used to list process.

The <mark style="color:red;">`ps`</mark> command supports three styles of options:

* **Traditional** **UNIX** style short options that **use a single hyphen in front of a character**.
* **GNU** style long options that **use two hyphens in front of a word**.
* **BSD** style options that use **no hyphens and single character options**

The <mark style="color:red;">`ps`</mark> command will display the processes that are running in the current terminal by default:

```bash
sysadmin@localhost:~$ ps
  PID TTY          TIME CMD
   80 pts/0        00:00:00 bash
   94 pts/0        00:00:00 ps
```

The output of the <mark style="color:red;">`ps`</mark> command includes the following columns of information:

| Column | Description                                                                                                                     |
| ------ | ------------------------------------------------------------------------------------------------------------------------------- |
| PID    | The process identifier, which is unique to the process.                                                                         |
| TTY    | The name of the terminal where the process is running. Useful to distinguish between different process that have the samen name |
| TIME   | The total amount of processor time used by the process                                                                          |
| CMD    | The command that started the process                                                                                            |

When the <mark style="color:red;">`ps`</mark> command is run with the **BSD style option**, the CMD column is replaced with the COMMAND column, which show not just the command, but also its options and arguments. **An additional column called STAT is also displayed**, which conveys(transmite) the state of the processes.

```bash
sysadmin@localhost:~$ ps x
  PID TTY      STAT   TIME COMMAND
   80 pts/0        S      0:00 -bash
   95 pts/0        R+     0:00 ps x
```

There are several states that a process can be in:

| State | Description           |
| ----- | --------------------- |
| D     | Uninterruptible Sleep |
| R     | Running               |
| S     | Interruptible sleep   |
| T     | Stopped               |
| Z     | Zombie                |

To **see all processes** (beloging to all usesr and not limited to the current shell) as well as displays the user owners of the processes, **use the **<mark style="color:red;">**`aux`**</mark>** BSD option**.

```bash
sysadmin@localhost:~$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  18376  2968 ?        Ss   16:41   0:00 /bin/bash /init
syslog       9  0.0  0.0 191328  3808 ?        Ssl  16:41   0:00 /usr/sbin/rsysl
root        13  0.0  0.0  28356  2656 ?        Ss   16:41   0:00 /usr/sbin/cron
root        15  0.0  0.0  72296  3272 ?        Ss   16:41   0:00 /usr/sbin/sshd
bind        24  0.0  0.0 1141532 39364 ?       Ssl  16:41   0:00 /usr/sbin/named
root        43  0.0  0.0  78636  3624 ?        S    16:41   0:00 /bin/login -f
sysadmin    56  0.0  0.0  18508  3436 ?        S    16:41   0:00 -bash
sysadmin    99  0.0  0.0  34400  2848 ?        R+   16:53   0:00 ps aux
```

| Option | Meaning                                                                                                                    |
| ------ | -------------------------------------------------------------------------------------------------------------------------- |
| `a`    | Allows the `ps` command to show all processes                                                                              |
| `u`    | **shows processes by all users** and ignores restrictions: to only list the current user's processes                       |
| `x`    | **List all processes** and removes the restriction: to only display the processes that are running in the current terminal |

Users may want to view every process running on the system. With traditional (non-BSD) options, **the **<mark style="color:red;">**`-e`**</mark>** option will display every process**. Typically,the <mark style="color:red;">**`-f`**</mark>** option is also used as it provides full details of the command**, inclluding options and arguments.

```bash
sysadmin@localhost:~$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 17:16 ?        00:00:00 /sbin??? /init
syslog      33     1  0 17:16 ?        00:00:00 /usr/sbin/rsyslogd
root        38     1  0 17:16 ?        00:00:00 /usr/sbin/cron
root        40     1  0 17:16 ?        00:00:00 /usr/sbin/sshd
bind        57     1  0 17:16 ?        00:00:00 /usr/sbin/named -u bind
root        70     1  0 17:16 ?        00:00:00 /bin/login -f
sysadmin    80    70  0 17:16 ?        00:00:00 -bash
sysadmin    96    80  0 17:26 ?        00:00:00 ps -ef
```

The <mark style="color:red;">`ps`</mark> command can be used along with the <mark style="color:red;">`grep`</mark> command to search for a specific process name. To find all processes that match the <mark style="color:red;">`sshd`</mark> string, you would type:

```bash
sysadmin@localhost:~$ ps aux | grep -i sshd
root        15  0.0  0.0  72296  3272 ?        Ss   16:41   0:00 /usr/sbin/sshd 
sysadmin   108  0.0  0.0  11464  1032 ?        S+   17:53   0:00 grep --color=auto -i sshd
```

### Searching for processes

The <mark style="color:red;">`pgrep`</mark> command parses through the running processes and looks for the specified names or other criteria of processes, rather than having to run the <mark style="color:red;">`ps`</mark> command, pipe its output to the <mark style="color:red;">`grep`</mark> command and then look at the results.

Users can simply use the <mark style="color:red;">**`pgrep`**</mark> command and various options to return what they need to find. If you wanted to find all instances of the `bash` command running on the system, you could use the following:

```
sysadmin@localhost:~$ pgrep -i sshd
1515
```

The output shows the Process IDs (PID) of the processes running that match the `bash` string. Additionaly the <mark style="color:red;">**`-l`**</mark> option can be used with the <mark style="color:red;">`pgrep`</mark> command to list the process name along with the PID:

```bash
sysadmin@localhost:~$ pgrep -li sshd
15 sshd
```

It's also possible to search for all the processes owned by a specific user by using the <mark style="color:red;">**`-u`**</mark> option:

```bash
sysadmin@localhost:~$ pgrep -u sysadmin -il
3330 kworker/2:2-events
3377 kworker/3:2-events
3430 kworker/1:0-events
3514 kworker/3:3-events
```

### Watching Processes

There are many times when it's advantageous to **see a process execute over and over again**. In those cases, the <mark style="color:red;">**`watch`**</mark> command can be used to monitor recurring processes by using the following syntax:

```
watch [OPTION]... COMMAND
```

A command that would normally execute and exit could be used with the <mark style="color:red;">**`watch`**</mark> **command to execute repeatedly.**&#x20;

```bash
sysadmin@localhost:~$ watch date
Every 2.0s: date                             localhost: Fri Mar 27 18:30:55 2020

Fri Mar 27 18:30:55 UTC 2020
```

By default, the <mark style="color:red;">**`watch`**</mark> command runs the given command **every 2 seconds** so the output of the `date` command will change to show a 2-second difference.

The <mark style="color:red;">**`watch`**</mark> command can also be used with the `ps` command to monitor processes in the shell:

```bash
sysadmin@localhost:~$ watch ps aux
```

Recall that the <mark style="color:red;">`watch`</mark> command's default interval is two seconds. Therefore, **to change the interval, use the **<mark style="color:red;">**`-n`**</mark>** option** followed by the specific interval desired (in seconds) after, and then specify the command to monitor.

```
watch -n # [COMMAND...]
```

The <mark style="color:red;">`watch`</mark> command can also be used to keep track of changes in a configuration file, log files or anything that changes over time. For example, to monitor changes to the last twenty lines of the `/var/log/ndg/web.log` log file and execute <mark style="color:red;">`tail`</mark> command every 15 seconds , use the following:

```bash
sysadmin@localhost:~$ watch -n 15 tail -n 20 /var/log/ndg/web.log
```

You can also highlight any differences between each successive execution of the program that is being watched by specifying the <mark style="color:red;">**`-d`**</mark> option.

![](<../.gitbook/assets/image (9).png>)

### Foreground Processes

So far, the commands that have been presented have been executing in what is known as the _foreground_. <mark style="background-color:red;">**A foreground process is one that prevents the user from using the shell until the process is complete**</mark>.

When a process starts another, **the first process is referred to as the **_<mark style="color:red;">**parent process**</mark>**,**_** and the new process is called a **_<mark style="color:red;">**child process**</mark>_. So another way of thinking of a foreground process is that when running in the foreground, a child process doesn't allow any further commands to be executed in the parent process until the child process ends.

### Executing multiple commands

Normally, users only type one command per command line, but using the semicolon <mark style="color:red;background-color:red;">**`;`**</mark> character as a delimiter between commands, **a user can type multiples commands on one command line**.

Another way of describing this is to say that the commands execute in sequence.

```
COMMAND;COMMAND[;COMMAND]...
COMMAND; COMMAND[; COMMAND]...
```

The <mark style="color:red;">`sleep`</mark> command will pause for a specified number of seconds before continuing to the next command.&#x20;

```bash
sysadmin@localhost:~$ echo Hello; sleep 5; echo World
Hello
World
```

Recall that an alias is a feature that allows a user to create nicknames for commands or command lines. An alias that runs multiple commands can be very useful; to acomplis this, the user would use the <mark style="color:red;">`;`</mark> character between each of the commands when creating the alias

```bash
sysadmin@localhost:~$ alias welcome="whoami;date;ls" 
```

{% hint style="info" %}
A real-life scenario involving multiple commands occurs when an administrator needs to take down and restart the network connection remotely. If the user typed the one command to bring down the network and then executed it, then the network connection would be terminated, and the user wouldn't be able to bring the network back up again. Instead, the user could type the command to take down the network, followed by a semicolon, then the command to bring up the network.
{% endhint %}

### Background Processes

When a command may take some time to execute, it may be better to have that command execute in the background. <mark style="color:red;">**To have a command execute as a background process, add the ampersand**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**`&`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**character after the command**</mark>:

```
COMMAND &
```

```bash
sysadmin@localhost:~$ sleep 3 & 
[1] 87
sysadmin@localhost:~$
[1]+  Done                    sleep 3
```

As show in the output, when executing commands in the background, after each command start executing, <mark style="background-color:red;">it outputs a</mark> <mark style="background-color:red;"></mark>_<mark style="background-color:red;">**job**</mark>_ <mark style="background-color:red;"></mark><mark style="background-color:red;">number followed by a space and then the</mark> <mark style="background-color:red;"></mark>_<mark style="background-color:red;">process identification (</mark><mark style="background-color:red;">**PID**</mark><mark style="background-color:red;">).</mark> _ These numbers are used for controlling the process.

> The `sleep` command in the preceding example has a job number of 1 and PID 87

While the are still **background processes** being run in the terminal, they **can be displayed by using the **<mark style="color:red;">**`jobs`**</mark>** command.**&#x20;

{% hint style="info" %}
The `jobs` command will only show background processes in the current terminal.
{% endhint %}

```bash
sysadmin@localhost:~$ sleep 1000 &
[1] 106
sysadmin@localhost:~$ sleep 2000 &
[2] 107
sysadmin@localhost:~$ jobs
[1]-  Running                 sleep 1000 &
[2]+  Running                 sleep 2000 &
```

The job number of a process is sometimes followed by a minus `-` or a plus `+` character. The plus **`+` character denotes the last process that was started**, while the **minus `-` character denotes a process started previous to the latest one.** All other jobs will have a blank in that space.

To **terminate the processes**, send them to the foreground by using the <mark style="color:red;">**`fg`**</mark> command with the job number of the process to terminate, and while the process is runnning in the foreground, **use** **Ctrl+C**

```bash
sysadmin@localhost:~$ fg 1
sleep 1000
^C
sysadmin@localhost:~$ fg 2
sleep 2000
^C
sysadmin@localhost:~$ jobs
sysadmin@localhost:~$
```

To have **multiple commands run in the background** on one command line, **place an ampersand **<mark style="color:red;">**`&`**</mark> character after each command on the command line.&#x20;

```bash
sysadmin@localhost:~$ echo Hello & echo World & echo '!' &
[1] 94
[2] 95
Hello
[3] 96
sysadmin@localhost:~$ World
!

[1]   Done                    echo Hello
[2]-  Done                    echo World
[3]+  Done                    echo '!'  
```

### Moving Processes

If the following <mark style="color:red;">**`sleep`**</mark> command is run without an ampersand character to send it to the background, the terminal would not be available for 1000 seconds:

```
sysadmin@localhost:~$ sleep 1000
_
```

To make the terminal available again, the user would have to use the <mark style="color:red;">**`Ctrl+Z`**</mark>, a <mark style="background-color:red;">**signal which pauses the process**</mark>**:**

```
^Z
[1]+  Stopped                 sleep 1000
```

Now the terminal is back, but the <mark style="color:red;">`sleep`</mark> command has been paused. To put the paused command in the background, execute the <mark style="color:red;">**`bg`**</mark> command:

```
bg [JOB_ID]...
```

{% hint style="warning" %}
The <mark style="color:red;">`bg`</mark> command resumes jobs without bringing them to the foreground. If no arguments is provided, it will use the most recently suspended job.
{% endhint %}

```
sysadmin@localhost:~$ bg
[1]+ sleep 1000 &
```

A command that has been paused or sent to the background can then be returned to the foreground using the <mark style="color:red;">**`fg`**</mark> command.

**Both the **<mark style="color:red;">**`fg`**</mark>** and **<mark style="color:red;">**`bg`**</mark>** can take the job number as an argument to specify which process should be resumed.**

It's also possible to use the name of the command as an argument to the <mark style="color:red;">`bg`</mark> and <mark style="color:red;">`fg`</mark> commands.

The <mark style="color:red;">`fg`</mark> and <mark style="color:red;">`bg`</mark> commands provide an administrator with the ability to manually multi-task.

### Sending a Signal

**A signal is a message that is sent to a process to tell the process to take some sort of action**, such as **stop**, **restart**, or **pause**. Signals are very useful in controlling the actions of a process.

Some signals can be sent to processes by simple keyboard combinations. For example, to have a foreground process **paused**, send a _Terminal Stop_ by pressing **Ctrl+Z**. A <mark style="background-color:red;">terminal stop</mark> <mark style="background-color:red;"></mark><mark style="background-color:red;">**pauses**</mark> <mark style="background-color:red;"></mark><mark style="background-color:red;">the program</mark> but does not completely stop the program. To completely stop a foreground process, send the _**Interrupt**_ signal by pressing **Ctrl+C.**&#x20;

{% hint style="info" %}
To have a process **paused**, press the **`Ctrl+Z`**\
To have a process **stopped**, press the **`Ctrl+C`**
{% endhint %}

To see a list of all the signals available for your system, execute the <mark style="color:red;">**`kill -l`**</mark> command:

```bash
sysadmin@localhost:~$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

All the signals with a number greater than 31 are for controlling real-time processes. Some of the more signals are summarized in the following table:

| Number | Full Name | Short Name | Purpose                                |
| ------ | --------- | ---------- | -------------------------------------- |
| `1`    | `SIGHUP`  | `HUP`      | Hang up, usually ends a process        |
| `2`    | `SIGINT`  | `INT`      | Interrupt, usually ends a process      |
| `3`    | `SIGQUIT` | `QUIT`     | Quit, usually ends a process           |
| `9`    | `SIGKILL` | `KILL`     | Kill, forcefully ends a process        |
| `15`   | `SIGTERM` | `TERM`     | Terminate, usually ends a process      |
| `18`   | `SIGCONT` | `CONT`     | Continue, resumes a stopped process    |
| `19`   | `SIGSTOP` | `STOP`     | Stop, forcefully stops a process       |
| `20`   | `SIGTSTP` | `TSTP`     | Terminal Stop, usually stops a process |

There are several commands that will allow you to specify a signal to send to a process; the <mark style="color:red;">`kill`</mark> command is the most commonly used. The syntax for the <mark style="color:red;">`kill`</mark> command looks like the following:

```
kill [OPTIONS]... <pid>
```

The <mark style="color:red;">`kill`</mark> command specifies which signal to be sent using options. It accepts three different ways to specify the signal:

```bash
-<signal>
-s <signal>
--signal <signal>

#The signal can be specified by using the number, the short name, or the full
#name.
-2
-INT
-SIGINT 
```

If the user doesnt' specify a signal with an option, then the <mark style="color:red;">`kill`</mark> command defaults to sending the _Terminate_ `SIGTERM` signal.

When sending a signal, specify one or more processes to send the signal to. There are numerous techniques to specify the process or processes. The more common techniques include:

* Specifying the **process identifier (PID)**
* Using the `%` prefix to the **job number**

```bash
sysadmin@localhost:~$ sleep 5000&
[1] 2901

sysadmin@localhost:~$ kill 2901
sysadmin@localhost:~$ kill %1
```

As indicated earlier, the **`SIGTERM`** signal normally **will** **end a process**. Sometimes a process will _**trap**_ the Terminate signal so it may not end that process. A user could try to use other signals, like `SIGQUIT` or `SIGINT`, to try to end a process, but these signals can also be trapped.

{% hint style="info" %}
**The only signal that will end a process and can't be trapped is a **<mark style="color:red;">**`SIGKILL`**</mark> signal. So, if other signals have failed to end a process, use the `SIGKILL` signal to force the process to end.
{% endhint %}

{% hint style="danger" %}
Users should not normally use the `SIGKILL` signal as the initial attempt to try to end a process because this forces the process to end immediately and will not allow the process the opportunity to "clean up" after itself
{% endhint %}

The following examples show two ways to send the Kill signal to a process:

```bash
sysadmin@localhost:~$ kill -9 2901
sysadmin@localhost:~$ kill -KILL %1
```

There are other commands that send processes signals, such as the <mark style="color:red;">**`killall`**</mark> and <mark style="color:red;">**`pkill`**</mark> commands, which are **useful to stop many processes at once**.

**The **<mark style="color:red;">**`pkill`**</mark>** command can be used to terminate one or more processes by name and other criteria such as the terminal and UID.** Similar to the <mark style="color:red;">`pgrep`</mark>, the <mark style="color:red;">`pkill`</mark> command allows users to specify a pattern that matches a process name and then sends signals to that process.&#x20;

```
pkill [OPTIONS]... PATTERN
```

The <mark style="color:red;">`killall`</mark> command **can also be used to terminate one or more processes by name** and other criteria **such as the user owner of the process and system processes**.

```
killall [OPTIONS]... NAME
```

Like the <mark style="color:red;">`kill`</mark> command, signals can be specified using either the number or signal name for both the <mark style="color:red;">`killall`</mark> and <mark style="color:red;">`pkill`</mark> commands. **These other commands can be used to terminate all processes of a particular user with the **<mark style="color:red;">**`-u`**</mark>** option**.

```bash
killall -u bob
```

The <mark style="color:red;">`killall`</mark> and <mark style="color:red;">`pkill`</mark> commands also accept the name of the process instead of a process or job number. Just be careful as this may end up stopping more processes than you had expected.

### HUP signal

When a user **logs off the system**, all processes that are owned by that user are automatically sent the _Hang Up_ <mark style="color:red;">**`SIGHUP`**</mark> signal. Typically, this signals **causes those processes to end**.

In some cases, **a user may want to execute a command that won't automatically exit** when it is sent a `HUP` signal. To have a process ignore Hang Up signal, start the process with the <mark style="color:red;">**`nohup`**</mark> command.

```
nohup COMMAND [ARG]...
```

For example, consider a scenario where a user has a script that needs to continue to run all night long. The user should start that script in the background of the terminal by executing

```bash
sysadmin@localhost:~$ nohup myjob.sh &
```

After executing the script, the user could proceed to log out. The next time the user logs in, the output of the script, if any, would be contained in the nohup.out file in that user's home directory.

### Process Priority

When a process runs, it needs to have access to the CPU to perform actions. Not all processes have the same access to the CPU.&#x20;

The linux kernel dynamically adjusts the priority of processes to try to make the operating system seem responsive to the user and efficent at performing tasks. **A user can influence the priority that will be assigned to a process by setting a value of something called **_**niceness.**_

![](<../.gitbook/assets/image (7).png>)

<mark style="background-color:red;">**The higher you set the niceness value, the lower the priority that will be assigned to a process**</mark>. The default value of niceness for processes is zero. Only a user with administrative (root) access can set negative values or alter the niceness of an existing process to be a lower niceness value.

To set the initial niceness of a command, use the <mark style="color:red;">`nice`</mark> command as a prefix to the command to execute.

```
nice [OPTION] [COMMAND [ARG]...]
```

The <mark style="color:red;">`-n`</mark> option indicates the desired niceness value.

```bash
sysadmin@localhost:~$ nice -n 19 cat /dev/zero > /dev/null
```

If a user logs in as the root user, they could also execute a command with the highest priority possible by executing the following command

```
sysadmin@localhost:~$ su -
Password:
root@localhost:~# nice -n -20 cat /dev/zero > /dev/null
```

To adjust the niceness of an existing process, use the <mark style="color:red;">`renice`</mark> command. To acomplish this, the user would need to discover the process ID for the process by using the `ps` command.

Next, use the <mark style="color:red;">`renice`</mark> command to adjust the priority back to normal. Like the `nice`command, the <mark style="color:red;">**`-n`**</mark> option indicates the niceness value. The <mark style="color:red;">`-p`</mark> option indicates the process ID to operate on.as

```bash
root@localhost:~# renice -n 0 -p 121
121 (process ID) old priority -20, new priority 0
```

### Monitoring Processes

While the <mark style="color:red;">`ps`</mark> command can display active processes, the <mark style="color:red;">`top`</mark> command provides the ability to **monitor processes in real-time**, as well as manage the processes. By default, the output of the <mark style="color:red;">`top`</mark> command will update every three seconds.&#x20;

```bash
sysadmin@localhost:~$ top
```

```bash
top - 16:47:34 up 51 days,  2:12,  1 user,  load average: 1.37, 1.56, 1.49
Tasks:  13 total,   4 running,   9 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us, 37.2 sy,  0.2 ni, 62.1 id,  0.0 wa,  0.1 hi,  0.0 si,  0.0 st
KiB Mem:  16438128 total, 13108516 used,  3329612 free,     4276 buffers
KiB Swap:        0 total,        0 used,        0 free.  9808716 cached Mem
 
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
  164 root      20   0    4364    696    616 R  87.4  0.1   1:23.32 cat
  165 root      30  10    4364    696    620 T   9.3  0.1   0:49.13 cat
  166 root      39  19    4364    772    696 T   1.7  0.1   0:41.75 cat
    1 root      20   0   17960   2972   2724 S   0.0  0.0   0:00.02 init
   33 syslog    20   0  255844   2728   2296 S   0.0  0.0   0:00.03 rsyslogd
   38 root      20   0   23656   2288   2076 S   0.0  0.0   0:00.00 cron
   40 root      20   0   61364   3124   2444 S   0.0  0.0   0:00.00 sshd
   57 bind      20   0  689640  29580   5328 S   0.0  0.2   0:00.13 named
   70 root      20   0   63132   2900   2452 S   0.0  0.0   0:00.00 login
   80 sysadmin  20   0   18176   3384   2896 S   0.0  0.0   0:00.04 bash
  151 root      20   0   46628   2708   2360 S   0.0  0.0   0:00.01 su
  152 root      20   0   18180   3388   2896 S   0.0  0.0   0:00.01 bash
  167 root      20   0   19860   2452   2124 R   0.0  0.0   0:00.00 top
```

Upon closer investigation of the niceness column `NI` and the CPU usage percentage column `%CPU` in the output above, we can see that the `cat` command with the lowest niceness (`0`) is using the highest percentage CPU (`87.4`), while the `cat` command with the highest niceness (`19`) is using the lowest percentage CPU (`1.7`).

The `top` command has numerous features; for example, it can be manipulated in an interactive manner. Pressing the **H** key while the `top` command is running will result in it displaying a help screen.

The **k** and **r** keys are used to manage tasks or processes within the `top` program.

Pressing the **k** key will allow a user to kill or send a signal to a process.&#x20;

Pressing the **r** key will allow a user to renice a process by prompting for the PID and then the new niceness value.

## Monitoring the system

There are also a couple of commands that can be **used to monitor the overall state of the system**: the <mark style="color:red;">**`uptime`**</mark> and <mark style="color:red;">**`free`**</mark> commands.

The <mark style="color:red;">`uptime`</mark> command shows the current time and the amount of time the system has been running, followed by the number of users who are currently logged in and the load averages during the past 1, 5 and 15 minutes intervals.

To get an idea of how busy a system is, use the <mark style="color:red;">`uptime`</mark> command:

```bash
sysadmin@localhost:~$ uptime
18:24:58 up 5 days, 10:43,  1 user,  load average: 0.08, 0.03, 0.05
```

The numbers reported for the load averages are based upon how many CPU cores are available. Think of each processor as having 100% resources (CPU times) available. The <mark style="color:red;">`uptime`</mark> command is reporting the amount of resources used, but it is dividing by 100. So, 1.00 is actually 100% of the CPU time being used, 2.00 is 200% and so on.

If a system has only one CPU core, the a value of 1.00 indicates that the system was fully loaded with tasks. If the system has two CPU cores, the a value of 1.00 would indicate 50% load.  An <mark style="color:red;">`uptime`</mark> reading of 1.00 (or 100%) usage on a 4 core CPU would imply that 1.00/4.00 (1/4 or 25%) of the CPU's total computational resources are being used.

{% hint style="info" %}
If you don't know how many CPU cores are available on a Linux system, **the **<mark style="color:red;">**`lscpu`**</mark>** command can be used to see how many CPU cores are available on the system**.
{% endhint %}

To get an idea of how your system is using memory, the <mark style="color:red;">`free`</mark> command is helpful. This command displays not only the values for the random-access memory (RAM) but also for swap, which is space on the hard disk that is used as memory for idle processes when the random-access memory starts to become scarce.

The <mark style="color:red;">`free`</mark> command reports the total amount of memory, as well as how much is currently being used and how much is free (available) for use. The output also breaks down the use of memory for buffers and caches:

{% hint style="info" %}
If logged into the Gnome desktop environment, the user can use the <mark style="color:red;">`gnome-system-monitor`</mark> program.&#x20;

The K desktop environment (KDE) has a similar program, the <mark style="color:red;">`ksysguard`</mark> command. The Gnome desktop environment is not available in the virtual environment for this chapter.
{% endhint %}

## Multi-session Command Line Utilities

You can manage multiple processes inside a single Bash shell environment.&#x20;

There are couple of options to accomplish true multiple terminal session productivity when limited to a single Bash shell connection on a system. We will focus on the <mark style="color:red;">`screen`</mark> and <mark style="color:red;">`tmux`</mark> commands, both of which allow the user to start processes in a session that can be detached while still running, the re-attached and managed by various methods.

### `screen` Command

The <mark style="color:red;">**`screen`**</mark> command also **allows you to run commands and control the command's environment** from within a <mark style="color:red;">**`screen`**</mark> session.&#x20;

The <mark style="color:red;">`screen`</mark> command **allows for multiple processes to run within separate sessions under a single terminal.** By default, the <mark style="color:red;">`screen`</mark> command provides a new session with a shell in it where a command can be executed with the ability to detach from that session and return the shell to start new sessions.

```
sysadmin@localhost:~$ screen
```

To verify that there is a <mark style="color:red;">`screen`</mark> session running, use the <mark style="color:red;">`-list`</mark> option to the <mark style="color:red;">`screen`</mark> command

```bash
sysadmin@localhost:~$ screen -list
There is a screen on:
        85.console.localhost   (03/30/19 14:33:05)     (Attached)
1 Socket in /run/screen/S-sysadmin.
```

Notice the text ( _Attached_ ) in the output of the previous command, which indicates that the session is the one the user's is currently _attached to_; in other words; the user is in that <mark style="color:red;">`screen`</mark> session.

To attach and detach a <mark style="color:red;">`screen`</mark> session, as well as perform other actions in the <mark style="color:red;">`screen`</mark> program, you will need to use the <mark style="color:red;">`screen`</mark> _command keys._

{% hint style="info" %}
To view a **list of the **<mark style="color:red;">**`screen`**</mark>** commands**, type <mark style="background-color:red;">**Ctrl + A**</mark> and then a question mark **`?`** while in screen session
{% endhint %}

Pressing the prefix **Ctrl+A** gets the attention of the <mark style="color:red;">`screen`</mark> command, and then the command key <mark style="color:red;">`d`</mark> issues the detach command, which detaches the <mark style="color:red;">`screen`</mark> command fromt the current terminal and returns the user to the original shell prompt.

```
[detached from 85.console.localhost]
sysadmin@localhost:~$
```

The format of identification for a <mark style="color:red;">`screen`</mark> process is the following:

```
PID.TTY.HOST
```

The first set of the character PID indicates the PID of the process, TTY means the terminal type the process is running in, and HOST is the hostname, or process name if changed using the <mark style="color:red;">`-S`</mark> option.&#x20;

It is also possible to run a command at the shell prompt in a <mark style="color:red;">`screen`</mark> session and then immediately detach that session in order to continue conducting other operations at the prompt. For example, start the `nano` text editor in a <mark style="color:red;">`screen`</mark> session and detach it with the following command:

```bash
sysadmin@localhost:~$ screen -S nano_edit -d -m nano
```

The command above will start the `nano` text editor in a detached <mark style="color:red;">`screen`</mark> session named `nano_edit` and drop the user back to a shell prompt. The session is named using the session <mark style="color:red;">**`-S`**</mark> option, which is followed by the desired session name as an argument. The <mark style="color:red;">`-d -m`</mark> option (is a single option) specifies to start the session in detached mode.

```
sysadmin@localhost:~$ screen -list
There are screens on:
        128.nano_edit   (03/31/19 18:39:24)     (Detached)
        85.console.localhost   (03/31/19 18:18:43)     (Detached)
2 Sockets in /run/screen/S-sysadmin.
```

The user can re-attach to either of the sessions by using the resume <mark style="color:red;">**`-r`**</mark> option with either the PID of the session or by the name of the session.&#x20;

```
sysadmin@localhost:~$ screen -r 85
```

To get rid of a <mark style="color:red;">`screen`</mark> session, attach to each session, and quit the program that is running. For example, the `nano` session can be re-attached using the <mark style="color:red;">`screen`</mark> command below

```
sysadmin@localhost:~$ screen -r nano_edit
```

Press **`Ctrl+X`** to exit the `nano` command and follow the prompts. When the `nano` process has exited, there will be no `nano_edit` `screen` session in the `- list` output.

```bash
sysadmin@localhost:~$ screen -list
There is a screen on:
        85.console.localhost   (03/30/19 14:55:05)     (Attached)
1 Socket in /run/screen/S-sysadmin
```

To exit the <mark style="color:red;">`screen`</mark> command, use the `exit` command

```
[screen is terminating]                                                         
sysadmin@localhost:~$
```

### `tmux` Command

The <mark style="color:red;">`tmux`</mark> command allows for multiple terminals to be opened and viewed on the same screen. Is similar to `screen` command, but **layers on more visual aspects**.

To start using the <mark style="color:red;">`tmux`</mark> command, execute the command at the prompt.

```
sysadmin@localhost:~$ tmux
```

To detach from the running command , press **Ctrl+B**, then the **d** command key. This will return the user to a shell session as well as indicate that the user is detached and is back at the shell prompt.

```
[detached (from session 0)]]
sysadmin@localhost:~$
```

The <mark style="color:red;">**`tmux`**</mark> command uses a prefix key, the combination of the **Control** key plus a letter key, in this case, the **B** key. The **Ctrl+B** key sequence prefixes all <mark style="color:red;">**`tmux`**</mark> in-session commands.

To find out what <mark style="color:red;">`tmux`</mark> sessions currently exist, use either of the commands below:

```
tmux list-sessions
tmux ls
```

To create a new <mark style="color:red;">`tmux`</mark> session and run a new command in it, use the <mark style="color:red;">**`tmux new-session`**</mark> command.

Then, to detach from the <mark style="color:red;">`tmux`</mark> session, press the **Ctrl+B** key sequence and then the **d** key, to be returned to the shell prompt again.

To re-attach to a running <mark style="color:red;">`tmux`</mark> session, use the <mark style="color:red;">**`tmux attach`**</mark> command with the target-session <mark style="color:red;">**`-t`**</mark> flag which specifies the session to act upon:

```
sysadmin@localhost:~$ tmux attach -t 0
```

To kill sessions without returning to it, use the following command with the target-session <mark style="color:red;">`-t`</mark> flag and the number of the session to be killed:

```
sysadmin@localhost:~$ tmux kill-session -t 1
```

The session or window indicator numeral should correspond to the session or window that is desired to be ended; in this case it is the indicator numeral `1`

