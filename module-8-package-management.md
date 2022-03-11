---
description: 'Weight: 6'
---

# Module 8: Package Management

<details>

<summary>Key Terms</summary>

**`/etc/apt/sources.list`** The package resource list used to locate archives of the package distribution system in use on the system

**`/etc/yum.conf`** Configuration file for the YUM package manager.

**`/etc/yum.repos.d/`** The directory housing YUM configuration files.

**`apt-cache`** Performs a variety of operations on APT's package cache including operations to search and generate interesting output from the package metadata.

**`apt-get`** Command-line tool for handling packages.

**`dpkg`** A tool to install, build, remove and manage Debian packages.

**`dpkg-reconfigure`** Utility used to change the settings of individual packages that are registered durinng installation.

**`md5sum`** Command based on the MD5 algoritm which creates a 128-bit hash using the original file.

**`rpm`** A package manager, which can be used to build, install, query, verify, update and erase individual software packages.

**`rpm2cpio`** Takes an RPM package file and converts it to a cpio archive.

**`sha256sum`** Command that creates a 256-bit checksum number that can be used to verify ~~~~ a file.

**`sha512sum`** A command that creates a 512-bit checksum for verifying files.

**`yum`** Yellow Dog Updater - an interactive, automated update program which can be used for maintaining systems using rpm

**`zypper`** The command line tool for the ZYpp/Libzypp is the background package management engine.

</details>

## Intro

The two most common software management systems were started by two of the most populat Linux distro: **Red Hat** and **Debian**. Both system provide similar functionality including the ability to manually download and install a software program as well as automate the process.

Another Linux distro, **SUSE**, use the package management system developed by Red Hat as well as its own package management system called **Zypper**.

## RPM Package Management

Red Hat's package management system is based upon the file format used to package the software files used within that distribution, known as the **RPM Package Manager**.

RPM files are available in two formats: <mark style="color:red;">**`.src.rpm`**</mark> files and <mark style="color:red;">**`.rpm`**</mark> files. The first contain the source code that is used to build the binary `.rpm` files. The binary `.rpm` files are the ones that contain the compiled code that is ready to use.

The files that are used by RPM have a specific naming convention to help identify the package, its version number, its release identifier, and which architecture the software was built for.

```bash
x3270-x11-3.3.6-10.5.el6.i686.rpm
package_name-version-release.architecture.rpm
```

#### Package Name

```
x3270-x11
```

The name of the package may include a hyphen. The name ends where the version number begins; the version number is always a decimal delimited number.

#### Version number

```
3.3.6
```

This version number is set by the original developer of the software

#### Release Identifier

```
10.5.el6
```

Release identifier are chosen by the packager (not the software developers) of the software, which is normally the vendor of the distribution.

#### Architecture

```
i686
```

If a package contains binary code, then the computer architecture that the code was compiled on will be the final part of the file name before the `.rpm` file extension.

There are several architecture types:

| Code                                         | Architecture                                                                                                                |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:red;">**`noarch`**</mark> | Packages that **do not contain binary code**, such as those that **may only contain script files.**                         |
| <mark style="color:red;">**`src`**</mark>    | Packages that **have not been compiled**                                                                                    |
| <mark style="color:red;">**`i686`**</mark>   | Packages compiled to **run in 32-bit mode** on a Pentium 4 or later processor                                               |
| <mark style="color:red;">**`x86_64`**</mark> | Packages compiled to **run in 64-bit mode** on either an AMD 64-bit processor or an Intel EM64T 64-bit compatible processor |

### RPM Queries

Any user can perform RPM queries to display information about packages that are currently or not installed on the system. The difference between doing a query about an installed package versus an RPM file is two-fold:

* To query a **package that is not installed**, use the <mark style="color:red;">**`-p FILE`**</mark> option.
* To query an installed package ,use only the package name

To perform any RPM query, always use the <mark style="color:red;">**`-q`**</mark> option with the <mark style="color:red;">**`rpm`**</mark> command. To query for basic package information, use the <mark style="color:red;">**`-i`**</mark> option:

```bash
[sysadmin@localhost ~]$ rpm -qi bash
Name        : bash
Version     : 4.2.46
Release     : 31.el7
Architecture: x86_64
Install Date: Tue Dec  4 14:38:13 2018
Group       : System Environment/Shells
Size        : 3667773
```

To perform a similar query on the RPM file for bash, the <mark style="color:red;">**`-p`**</mark> option would be added along with the <mark style="color:red;">**`-q`**</mark> and <mark style="color:red;">**`-i`**</mark> option:

```
[sysadmin@localhost ~]$ rpm -qip bash-4.2.46-31.el7.src.rpm
```

{% hint style="info" %}
Always remember that RPM queries require the use of the <mark style="color:red;">**`-q`**</mark> option for **installed package** and the <mark style="color:red;">**`-qp`**</mark> for **uninstalled package files**.
{% endhint %}

| Option       | Purpose                                                                     |
| ------------ | --------------------------------------------------------------------------- |
| `-a`         | List all of the installed packages currently on the system                  |
| `-c`         | Display a list of configuration files that belong to the package            |
| `-d`         | List the documentation files that belong to the package                     |
| `-i`         | Display the package information                                             |
| `-K`         | Check the package integrity                                                 |
| `-l`         | List all files in the package                                               |
| `--provides` | List the capabilities this package provides                                 |
| `-R`         | List the capabilities that this package requires                            |
| `--scripts`  | List the scripts that are used before and after installation of the package |
| `-s`         | Display the state of each package file as normal, not installed or replaced |
| `-U`         | Install a package if doesn't exist or update it if is already installed     |
| `-F`         | Update a package already installed                                          |
| `h`          | Show the progress of the update or install                                  |
| `v`          | show the progress for every package                                         |
| `-e`         | Delete a package                                                            |
| `-V`         | Verify a package                                                            |
| `-f`         | Determine which package owns a file                                         |

#### Querying Scripts

It is a good idea to query a package to see the scripts it will execute before installing an RPM file from a third-party source.

Before installing the `x3270-x11` package, the administrator could view the scripts to ensure that they are not dangerous by executing the following command:

```
rpm -qp --scripts x3270-x11-3.3.6-10.5.el6.i686.rpm
```

#### Querying the Integrity of a Package

If an administrator is concerned with the security of the system, then not only should the administrator query a package to view its script, but also query the integrity of a package by using the <mark style="color:red;">**`-K`**</mark> option.

> To use this option on an `.rpm` file, import the public key file that is distributed by the same organization that packaged and distributed the `.rpm` file.

On Red Hat-derived distros, the public key files are stored in the **`/etc/pki/rpm-gpg`** directory. An administrator can import these keys into the RPM database with the command:

```bash
rpm --import /etc/pki/rpm-gpg/*
```

After the keys have been imported, then a package's integrity can be verified. For example, to verify the integrity of the `x3270-x11` package, an administrator could execute the following command:

```bash
[sysadmin@localhost ~]$ rpm -qpK x3270-x11-3.3.6-10.5.el6.i686.rpm
x3270-x11-3.3-4.el7.x86_64.rpm: rsa sha1 (md5) pgp md5 OK
```

#### Query for Installed Packages

If the exact name is known, simply query the package name. For example, to see if the `bash` package is installed, execute:

```bash
[sysadmin@localhost ~]$ rpm -q bash
bash-4.2.46-31.el7.src.rpm

[sysadmin@localhost ~]$ rpm -q pickle
package pickle is not installed
```

The **`*`** glob character may be useful **if the exact package name is not known**. For example, if you knew that the package name contained `python`, but you were unsure of the rest, then execute the following:

```bash
[sysadmin@localhost ~]$ rpm -qa *python*
python-2.6.6-52.el6.x86_64
python-urlgrabber-3.9.1-9.el6.noarch
rpm-python-4.8.0-37.el6.x86_64
python-libs-2.6.6-52.el6.x86_64
python-pycurl-7.19.0-8.el6.x86_64
```

{% hint style="info" %}
**Note**

To get an alphabetical list of all installed packages, execute the <mark style="color:red;">**`rpm -qa | sort`**</mark> command. To get a chronological list of all packages, execute the <mark style="color:red;">**`rpm -qa --last`**</mark> command.
{% endhint %}

### Installing Packages with `rpm`

A depency is a software package that is required for another package to be installed and function correctly.&#x20;

For example, to install the `x3270-x11-3.3.6-10.5.el6.i686.rpm` package, execute:

```
rpm -i x3270-x11-3.3.6-10.5.el6.i686.rpm
```

Most administrators determine package dependencies by examining the output of a failed RPM package install. However, it is also possible to check the requirements and dependencies of a package in advance with the following <mark style="color:red;">**`rpm`**</mark> query:

```
rpm -qpR x3270-x11-3.3.6-10.5.el6.i686.rpm
/bin/sh
/usr/bin/mkfontdir
gtk2 >= 2.6
libICE.so.6
libicuuc.so.42
libnsl.so.1
libssl.so.10
libutil.so.1
```

{% hint style="warning" %}
**Note**

To see what each packages provides, perform a query with the `--provides` option.

```
[sysadmin@localhost ~]$ rpm -qp --provides x3270-3.3.6-10.5.el6.i686.rpm
config(x3270) = 3.3.6-10.5.el6
x3270 = 3.3.6-10.5.el6
```
{% endhint %}

Normally, the `rpm` command will refuse to install a package that is already installed, but it can be forced by adding the `--force` option.

```
rpm --force -i x3270-3.3.6-10.5.el6.i686.rpm
```

### Erasing package with `rpm`

The <mark style="color:red;">**`rpm`**</mark> command can be used to erase (remove) packages from the system with the <mark style="color:red;">**`-e`**</mark> option.

The **`rpm`** command will not allows a package to be erased if it is a requirement of another package.

```
rpm -e x3270-3.3.6-10.5.el6.i686.rpm
```

### Updating pakacges with `rpm`

To update a package with a new version or newer release, use the `rpm` command with either the <mark style="color:red;">**`-U`**</mark> or the <mark style="color:red;">**`-F`**</mark> option.

The <mark style="color:red;">**`-U`**</mark> option  can be used with the `rpm` command to either install or update a package.

```
rpm -U x3270-3.3.6-10.5.el6.i686.rpm
```

On the other hand, when using the **`rpm`** command with the <mark style="color:red;">**`-F`**</mark> option, the package will only be updated if the package is already installed; this is called _freshening_ the package:

```
rpm -F x3270-3.3.6-10.5.el6.i686.rpm
```

### Using `rpm2cpio`

The <mark style="color:red;">**`rpm2cpio`**</mark> command accepts an `.rpm` file as an argument ( or reads data that is in a package format from standard input) and outputs a <mark style="color:red;">**`cpio`**</mark> archive.

{% hint style="info" %}
This capability can be used to list the contents of an `.rpm` file or to extract one or more files from within the original `.rpm` file.
{% endhint %}

For example, to list the contents of the `telnet-server-0.17-47.el6_3.1.i686.rpm` file, execute the following command.

```bash
rmp2cpio telnet-server-0.17-47.el6_3.1.i686.rpm | cpio -t
./etc/xinetd.d/telnet
./usr/sbin/in.telnetd
./usr/share/man/man5/issue.net.5.gz
./usr/share/man/man8/in.telnetd.8.gz
./usr/share/man/man8/telnetd.8.gz
512 blocks
```

> The output shows the files contained within the `.rpm` file and the total number of 512KiB blocks that those files would use if executed.

To copy these files from the `.rpm` file, use the following command to extract these files to the current directory.

```bash
rpm2cpio telnet-server-0.17-47.el6_3.1.i686.rpm | cpio -imud
```

Several options for the `cpio` command are listed in the table below:

| Option | Purpose                                                                 |
| ------ | ----------------------------------------------------------------------- |
| `-i`   | Extract                                                                 |
| `-m`   | Retain the original modification times on the files                     |
| `-u`   | Unconditionally replace any existing files                              |
| `-d`   | Create any parent directories of the files contained within the archive |

{% hint style="info" %}
**Consider This**

Why use the <mark style="color:red;">**`rpm2cpio`**</mark> command? Consider a situation where a key package file is accidentally removed from the system. Reinstalling the entire package may be overkill if only a single file is needed. Using the <mark style="color:red;">**`rpm2cpio`**</mark> command, the **file that is missing can be extracted and copied back** into the appropriate directory.
{% endhint %}

## Manage Packages with `yum`

The <mark style="color:red;">**`yum`**</mark> command can locate and download packages on the internet and **resolve dependencies automatically.**

> Although it is possible to resolve dependency issues manually with the `rpm` command, they can be solved automatically with the `yum` command.

The big advantage of the <mark style="color:red;">**`yum`**</mark>** ** command is that it can be configured to automatically download packages and resolved package dependencies. In addition, the <mark style="color:red;">**`yum`**</mark> command **can display package information for packages that aren't even on the system** by accessing this data from a server called repository.

Typically, the <mark style="color:red;">**`yum`**</mark> command is configured by editing the **`/etc/yum.conf`** file and the files found in the **`/etc/yum.repos.d`** directory.

{% hint style="info" %}
These configuration files are used to **specify servers (the repositories) on the internet** where the <mark style="color:red;">**`yum`**</mark> command can obtain the RPM files automatically.
{% endhint %}

{% hint style="danger" %}
If the system does not have networking enabled or **doesn't have access to the internet**, then it **will not be able to use** the <mark style="color:red;">**`yum`**</mark> command to manage packages. In those situations, the <mark style="color:red;">**`rpm`**</mark> command can be used for package management.
{% endhint %}

One **`rpm`** query that was not mentioned previously is the <mark style="color:red;">**`-f`**</mark> option:

```bash
rpm -qf /bin/bash
bash-4.1.2-15.el6_4.x86_64
```

This query is used to **determine which package owns** the `/bin/bash` **file**.&#x20;

{% hint style="warning" %}
This query only succeeds if the `bash` package is already installed on the system.
{% endhint %}

On the other hand, the `yum` command can be used to determine which package owns the file because the `yum` command searches the database of the repository servers

```
yum provides /usr/lib/libicuuc.so.42
```

Or a glob pattern can be used:

```bash
yum provides "*/libicuuc.so.42"
```

The other distinct advantage of using the **`yum`** command  is the **ability to search for a package in the repositories (installed or available for install)**, based upon the name or description of the package.

```bash
[sysadmin@localhost ~]$ yum search terminal
Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
 * base: centosb6.centos.org
 * extras: centosi5.centos.org
 * updates: centosb6.centos.org
============================ N/S Matched: terminal =============================
gnome-terminal.i686 : Terminal emulator for GNOME
nautilus-open-terminal.i686 : Nautilus extension for an open terminal shortcut
kbd.i696 : Tools for configuring the console (keyboard, virtual terminals, etc.)
minicom.i686 : A text-based modem control and terminal emulation program
ncurses-base.i686 : Descriptions of common terminals
ncurses-term.i686 : terminal descriptions
```

