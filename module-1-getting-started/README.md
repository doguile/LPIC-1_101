# Module 1: Getting Started

## Introduction

The definition of the word Linux depends on the context in which it is used. Technically speaking, <mark style="background-color:red;">**Linux is the Kernel of the system**</mark>, which is the central controller of everything that happens on the computer.

UNIX was originally an operating system developed at AT\&T Bell Labs in the 70s. It has been modified and _forked (_people modified it_)_ such that now there are many different variants of UNIX. However, **UNIX is now both a trademark and a specification, owned by Open Group**.

Only software that has been certified by the Open Group may call itself UNIX. So, **Linux has not been certified, so Linux really isn't UNIX.** It's just UNIX-like.

The 3 main components of an operating system are the <mark style="background-color:red;">**kernel, shell and filesystem.**</mark>

## Kernel

The kernel dictates which program gets which pieces of memory, it starts and kill programs, it interprets instructions given to it by the user, and it handles common and simple task.&#x20;

The kernel also handles the switching of applications. The kernel takes care of unloading tasks and loading new tasks, and can manage multiple tasks across multiple CPUs. **When the current task has run a sufficient amount of time , the CPU pauses the task so that another may run. This is called **_<mark style="background-color:red;">**preemptive multitasking.(Multi-tareas preventivo)**</mark>_

{% hint style="info" %}
_Multitasking_ means that the computer is doing several tasks at once,and _preemptive_ means that the kernel is deciding when to switch focus between tasks.
{% endhint %}

When the computer starts up, it loads a small piece of code called a **** _bootloader_. **The bootloader’s job is to give you a choice (if configured) of options to load one or more versions of Linux**, or even other operating systems, and then to load the kernel of the chosen option and get it started.

### Applications

Applications make requests to the kernel and receive resources, such as memory, CPU, and disk, in return. <mark style="background-color:red;">**Applications just follows the kernel's**</mark><mark style="background-color:red;">** **</mark>_<mark style="background-color:red;">**Application Programming Interface (API)**</mark> _ and in return don't have to worry about the implementations details.

The kernel doesn't care if it is running something that's user-facing, a network service that talks to remote computer, or an internal task. So, from this, **we get an abstraction called a **_**process**._&#x20;

#### Processes

A process is just one task that is loaded and tracked by the kernel. **An application may have even need multiple process to function**, so the **kernel takes care of running the processes**, starting and stopping them as requested, and handling out system resources.

### Role of Open Source

The linux kernel is mostly written in C, which is the language that shares history with the original UNIX.

Source code is not understood directly by the computer, so it must be compiled into machine instructions by a _compiler._ The compiler gathers all of the source files and generates something that can be run on the computer, such as the linux kernel.

The linux Kernel is licensed under the GNU Public License (GPL) which requires you to make changes available.

### Linux Distributions

The **distributions takes care of setting up the storage, installing the kernel, and installing the rest of the software**. The full-featured distributions also **include tools to manage the system and a **_**package manager**_ to help you add and remove software after the installation is complete.

The major players in the market can be traced back to either **Red Hat** or **Debian.**

Over time, Red Hat started to focus more on the server applications such as web and file serving and released Red Hat Enterprise Linux, which was a paid service on a long _release cycle_.&#x20;

{% hint style="info" %}
The release cycle dictates how often software is upgraded.
{% endhint %}

Red Hat sponsors the **Fedora Project** which makes a personal desktop comprising the latest software but still built on the same foundations as the enterprise version.

Because everything in Red Hat Enterprise Linux is open source, a project called **CentOS** came to be, that recompiled all the RHEL packages and gave them away for free.

**Debian** is more of a community effort, and as such, also promotes the use of open source software and adherence to standards. Debian came up with its own package management  system based on the _.deb_ file format.

**Ubuntu** is the most popular Debian-derived distribution. It is the creation of Cannonical and makes money by providing support.

## Shell

<mark style="background-color:red;">A shell is sometimes called an</mark> <mark style="background-color:red;"></mark>_<mark style="background-color:red;">interpreter</mark>_ because it takes the commands that a user issues and interprets them into a form that the kernel can then execute on the hardware of the computer. The 2 most common types of shells are the **Graphical Unit Interface (GUI)** and **Command Line Interface (CLI).**

There are many advantages to using a CLI, including:

* Command Repetition
* Command Flexibility
* Less Resources
* Scripting
* Remote Access
* Development

Recently, Microsoft realized how important it is to have a powerful command line environment and introduced **Powershell**.

Like Windows, Linux also has both a CLI and GUI. Unlike windows, _**linux lets you easily change the GUI shell**_** ** (also called desktop environment) that you want to use. The **two most common desktop** environments for Linux are **GNOME** and **KDE.**

To access the CLI from within the GUI on a Linux operating system, the user can open a software program called a _terminal_.

### Bash Shell

Multiple CLI also are available on Linux. Normally, these shells are derived from one of two older UNIX shells: the Bourne Shell and the C shell. In fact, the Bash shell, derives its name from the Bourne shell: **B**ourne **A**gainst **SH**ell.&#x20;

Users interact with a system by executing _commands_ which are interpreted by the shell and transformed into actions by the kernel. These actions may or may not return information to the command line depending on the command issued and its result.

A command can be followed by _`options`_ **that modify how the command is executed**, and _`arguments`_**, that are typically the files to be operated on**:

```
commands [options] [arguments]
```

Commands entered are considered _<mark style="background-color:red;">standard input (stdin)</mark>_, whether they are typed by an operator, entered by a script, or as the result of another command. Text returned to the console can be either <mark style="background-color:red;">standard output (stdout)</mark>, or _<mark style="background-color:red;">standard error (stderr)</mark>_.

{% hint style="warning" %}
An administrator can use the <mark style="color:red;">`usermod`</mark> command to specify a different default shell after the account has been created.

As user, you can use the <mark style="color:red;">`chsh`</mark> command to change your default shell.

The location where the system stores the default shell for user accounts is the **`/etc/passwd`** file.
{% endhint %}

### Accessing the shell

How you access the command line shell depends on whether your system provides a GUI login or CLI login:

* GUI-based system
* CLI-based system

If you are in a remote location, then pseudo-terminal connections can also be made across the network using several techniques. Insecure connections could be made using protocols such as <mark style="color:red;">`telnet`</mark> and programs such as <mark style="color:red;">`rlogin`</mark>, while secure connections can be established using programs like <mark style="color:red;">`putty`</mark> and protocols such as ``` `<mark style="color:red;">`ssh.`</mark>

## Filesystems

To the user, a filesystem is a hierarchy of directories and files with the root `/` directory at the top of the directory tree. To the operating system, a filesystem is a structure created on a disk partition consisting of tables defining the locations of directories and files.
