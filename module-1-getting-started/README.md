---
description: '103.1: Work on the command line v2  Weight: 4'
---

# Module 1: Getting Started

<details>

<summary>Key Terms</summary>

**`bash`** Bourne Again SHell - an sh-compatible command language interpreter that executes commands read from the standard input or  from a file

**`ls`** Command that will list information about files. The current directory is listed by default.

</details>

## CHAPTER 1: INTRODUCTION

## Introduction

The definition of the word Linux depends on the context in which it is used. Technically speaking, **Linux is the Kernel of the system**:thumbsup:, which is the central controller of everything that happens on the computer.

UNIX was originally an operating system developed at AT\&T Bell Labs in the 70s. It has been modified and _forked (_people modified it_)_ such that now there are many different variants of UNIX. However, **UNIX is now both a trademark and a specification, owned by Open Group**.

Only software that has been certified by the Open Group may call itself UNIX. So, **Linux has not been certified, so Linux really isn't UNIX.** It's just UNIX-like or GNU/Linux.

The 3 main components of an operating system are:

* Kernel
* Shell
* Filesystem.

## Kernel

The kernel **dictates which program gets which pieces of memory**, it starts and kill programs, it interprets instructions given to it by the user, and it handles common and simple task.&#x20;

The kernel also **handles the switching of applications**. The kernel takes care of unloading tasks and loading new tasks, and can **manage multiple tasks across multiple CPUs**. **** When the current task has run a sufficient amount of time , the CPU pauses the task so that another may run**.** This is called _preemptive_ multitasking

{% hint style="info" %}
_**Multitasking**_ means that the computer is doing **several tasks at once**,and _**Preemptive**_ means that the kernel is deciding **when to switch focus between tasks**.
{% endhint %}

When the computer starts up, it loads a small piece of code called a ** **_<mark style="color:red;">**bootloader**</mark>_. **The bootloader’s job is to give you a choice (if configured) of options to load one or more versions of Linux**, or even other operating systems, and then to load the kernel of the chosen option and get it started.

### Applications

Applications **make requests to the kernel and receive resources**, such as memory, CPU, and disk, in return. Applications just follows the kernel's _API **** (Application Programming Interface)_ and in return don't have to worry about the implementations details.

The kernel doesn't care if it is running something that's user-facing, a network service that talks to remote computer, or an internal task. So, from this, **we get an abstraction called a **_<mark style="color:red;">**process**</mark>._&#x20;

#### Processes

A process is just one **task that is loaded and tracked by the kernel**. **** An application may have even need multiple process to function, so the **kernel takes care of running the processes**, starting and stopping them as requested, and handling out system resources.

### Role of Open Source

**The linux kernel is mostly written in `C`**, which is the language that shares history with the original UNIX.

Source code is not understood directly by the computer, so it must be compiled into machine instructions by a _<mark style="color:red;">compiler</mark>._ The compiler gathers all of the source files and generates something that can be run on the computer, such as the linux kernel.

The linux Kernel is licensed under the GNU Public License (GPL) which requires you to make changes available.

### Linux Distributions

The **distributions takes care of setting up the storage, installing the kernel, and installing the rest of the software**. The full-featured distributions also **include tools to manage the system and a **_**package manager**_ to help you add and remove software after the installation is complete.

The major players in the market can be traced back to either **Red Hat** or **Debian.**

Over time, **Red Hat started to focus more on the server applications** such as web and file serving and released Red Hat Enterprise Linux, which was a paid service on a long _release cycle_.&#x20;

{% hint style="info" %}
The release cycle dictates how often software is upgraded.
{% endhint %}

#### Red Hat

Red Hat sponsors the **Fedora Project** which makes a personal desktop comprising the latest software but still built on the same foundations as the enterprise version.

Because everything in Red Hat Enterprise Linux is open source, a project called **CentOS** came to be, that recompiled all the RHEL packages and gave them away for free.

#### **Debian**

Debian is more of a community effort, and as such, also promotes the use of open source software and adherence to standards. Debian came up with its own package management  system based on the _.deb_ file format.

**Ubuntu** is the most popular Debian-derived distribution. It is the creation of Cannonical and makes money by providing support.

## Shell

A shell is sometimes called an _<mark style="color:red;">interpreter</mark>_ because it takes the commands that a user issues and interprets them into a form that the kernel can then execute on the hardware of the computer. The 2 most common types of shells are the **Graphical Unit Interface (GUI)** and **Command Line Interface (CLI).**

There are many advantages to using a CLI, including:

* Command Repetition
* Command Flexibility
* Less Resources
* Scripting
* Remote Access
* Development

Recently, **Microsoft** realized how important it is to have a powerful command line environment and **introduced **_**`Powershell`**_.

Like Windows, Linux also has both a CLI and GUI. Unlike windows, _**linux lets you easily change the GUI shell**_** ** (also called desktop environment) that you want to use. **The** **two most common desktop** environments for Linux are **GNOME** and **KDE.**

To access the CLI from within the GUI on a Linux operating system, the user can open a software program called a _<mark style="color:red;">terminal</mark>_.

### Bash Shell

Multiple CLI also are available on Linux. Normally, these shells are derived from one of two older UNIX shells: the Bourne Shell and the C shell. In fact, the Bash shell, derives its name from the Bourne shell: **B**ourne **A**gainst **SH**ell.&#x20;

Users interact with a system by executing _commands_ which are interpreted by the shell and transformed into actions by the kernel. These actions may or may not return information to the command line depending on the command issued and its result.

A command can be followed by _<mark style="color:red;">`options`</mark>_ **that modify how the command is executed**, and _<mark style="color:red;">`arguments`</mark>_**, that are typically the files to be operated on**:

```
commands [options] [arguments]
```

Commands entered are considered standard **input** (<mark style="color:red;">`stdin`</mark>), whether they are typed by an operator, entered by a script, or as the result of another command. Text returned to the console can be either standard **output** (<mark style="color:red;">`stdout`</mark>), or standard **error** (<mark style="color:red;">`stderr`</mark>).

{% hint style="warning" %}
An administrator can use the <mark style="color:red;">**`usermod`**</mark> command to **specify a different default shell** after the account has been created.

As **user**, you can use the <mark style="color:red;">**`chsh`**</mark> command to **change your default shell.**

The location where the system stores the **default shell for user accounts** is the **`/etc/passwd`** file.
{% endhint %}

### Accessing the shell

How you access the command line shell depends on whether your system provides a GUI login or CLI login:

* GUI-based system
* CLI-based system

If you are in a remote location, then pseudo-terminal connections can also be made across the network using several techniques. **Insecure connections** could be made using protocols such as <mark style="color:red;">`telnet`</mark> and programs such as <mark style="color:red;">`rlogin`</mark>, while **secure connections** can be established using programs like <mark style="color:red;">`putty`</mark> and protocols such as <mark style="color:red;">`ssh`</mark><mark style="color:red;">.</mark>

## Filesystems

To the user, a filesystem is a hierarchy of directories and files with the root **`/`** directory at the top of the directory tree. To the operating system, a filesystem is a structure created on a disk partition consisting of tables defining the locations of directories and files.
