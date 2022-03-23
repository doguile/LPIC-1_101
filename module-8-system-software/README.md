---
description: 'Weight: 6'
---

# Module 8: System Software

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

## Chapter 24: Package Management

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

## Installing Packages with DNF

Dandified Yum, or DNF, is the next upcoming version of the `yum` command. <mark style="color:orange;">**DNF is designed to solve package management dependency issues**</mark>, also referred to as _depsolve_ problems, that were present in `yum`.

Another benefit of DNF over `yum` is a <mark style="color:orange;">**clearley documented Application Programming Interface (API)**</mark>. The API is the interface that allows applications to work with other application. DNF is <mark style="color:orange;">**designed with a strict API for plugins and extensions**</mark> and it mostly maintains command line compatibility with `yum`.

{% hint style="info" %}
In the case of DNF, a plugin refers to software that provides extra functionality to DNF and modifies the DNF installation itself.

Extensions are programs that import DNF functionality through DNF's python libraries. They do not modify the DNF installation.
{% endhint %}

To start using DNF, the `dnf` command can be executed using the following syntax:

```
dnf [OPTIONS] <COMMAND> [<ARGUMENTS>...]
```

The <mark style="color:red;">**`dnf`**</mark> command uses the <mark style="color:red;">**`list`**</mark> command with the <mark style="color:red;">**`--available`**</mark> argument to list all available packages on the system:

```bash
dnf list --available
Last metadata expiration check: 0:25:49 ago on Sat Jun 22 23:41:27 2019.
Available Packages
cowsay.noarch                    3.04-4.el7                     
```

The output above indicates that the `cowsay.noarch` package is available for install. To install the `cowsay.noarch` package, the `install` option can be used:

```bash
dnf install cowsay.noarch
added from: file:///mnt/local_repo/             935 kB/s | 1.6 kB     00:00
Dependencies resolved.
================================================================================
 Package        Arch           Version            Repository               Size
================================================================================
Installing:
 cowsay         noarch         3.04-4.el7         mnt_local_repo_          42 k

Transaction Summary
================================================================================
Install  1 Package
```

To verify that the `cowsay.noarch` package was successfully installed, list the recently installed packages using the `list` command with the `--installed` option:

```bash
dnf list --installed
```

## Debian Package Management

Debian's package management system is based upon the format used to package the software files for the Debian distribution; **these package file names end in the **<mark style="color:red;">**`.deb`**</mark>** extension**. These `.deb` files are archives that contain the software and the information to configure it for installation or removal.

{% hint style="info" %}
**The **<mark style="color:red;">**`.deb`**</mark>** packages contain binary files compiled to execute** on a particular computer architecture. In the Debian's package management system, **the source packages**, which contain the original source, **have the **<mark style="color:red;">**`.dsc`**</mark>** extension**.
{% endhint %}

The primary configuration files that is used to **locate these repositories is the **<mark style="color:red;">**`/etc/apt/source.list`**</mark>** file.**

Before working with the packages, run the `apt-get update` command. This will ensure that the system has a current list of packages that are available in the repositories listed in the `/etc/apt/source.list` file.

Debian packages files follow a naming convention that makes it easy to determine the package name, version, release, and architecture. For example, the `bat_0.20.0_amd64.deb` file uses this generic pattern:

```
package-name_version-release_architecture.deb
```

#### Package Name

`bat_0`The package name is the first part of the file name up to ther first underscore \_ character.

#### Version

`20.0` The version is the second part of the file name. It start after the first underscore and continues unilt a hyphe - character is found.

#### Release

The third part of the file name is the release. The release is set by the organization that packages and distributes the package file.

#### Architecture

`amd64`The final part of the file name after the release, but before `.deb` is the architecture for which the package was compiled. Not only does Debian support multiple PC architectures, including `amd64`, `i386`, and `ia64` ,but numeroues other architectures like armel, armhf, mips, mipsel, powerpc, s390, s390x, and sparc.

The <mark style="color:red;">**`dpkg`**</mark> command is the most direct tool availabel for managing `.deb` packages. It can be used for installing, removing, and querying packages information, although other higher-level tools like <mark style="color:red;">**`synaptic`**</mark> ,or the **Advanced Package Tool (APT)** provide more powerful capabilities for managing dependencies between packages.

The <mark style="color:red;">**`synaptic`**</mark> command is a graphical user interface that allows an administrator to install, remove, and update packages by navigation menus.

### Installing Software with `dpkg`

To install a software package in a Debian-based distribution, use the `-i` option with the `dpkg` command.&#x20;

```
dpkg -i joe_3.7-2.3_i386.deb
```

Debian packages also may have dependencies. There are four categories of dependencies: depends, recommends, suggests, and enhances.&#x20;

### Removing software with the `dpkg`

There are a couple of ways that an administrator may remove a package, using the `-r` and `-P` options to the <mark style="color:red;">**`dpkg`**</mark> command.&#x20;

The <mark style="color:red;">**`-r`**</mark> options **removes** the package and the <mark style="color:red;">**`-P`**</mark> option **purges** the package. When a package is removed, almost all of its file are removed except for its configuration files. When a package is purged, all of the packages file are removed, including the configuration files.

### Listing packages with the `dpkg`

The `dpkg` command can retrieve a list of packages that are installed on the system, or list the files that are in a package. For example, to see all the packages that are currently installed on a system, execute the `dpkg -l` command:

```bash
sysadmin@localhost:~$ dpkg -l
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-=================================
ii  adduser        3.113+nmu3ub all          add and remove users and groups
ii  apt            1.0.1ubuntu2 amd64        command-line package manager
ii  apt-file       2.5.2ubuntu1 all          search for files within Debian pa
```

The output of the <mark style="color:red;">**`dpkg -l`**</mark> command contains five pieces of information:

* The first two pieces of this information are represented by a two-letter code:
  * The **first letter reflect the desired status of the package**, which can be `i` for installed, `u` for unknown, `r` for remove of `h` for hold.
  * The **second letter represents the actual status of the package**, where `i` means installed, and `n` means not installed.
  * The most common two-letter codes are **`ii`** for fully installed and **`un` ** for not installed.&#x20;
* The remaining columns show the package name, version, the architecture, and the description of the package if it is available.

The `dpkg -l PACKAGE-NAME-PATTERN` command can also be used to list packages based on a glob pattern. For example, to view all packages that might contain `perl` in the package name, use the `dpkg -l "*perl*"` command.

```
sysadmin@localhost:~$ dpkg -l "*perl*"
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-=================================
un  dh-make-perl   <none>       <none>       (no description available)
un  libalien-wxwid <none>       <none>       (no description available)
```

Using the <mark style="color:red;">**`-L`**</mark> option with the `dpkg` will <mark style="color:orange;">**list the files that a package contains**</mark>. For example to view, the files that are a part of the package named `perl` ,execute the <mark style="color:red;">**`dpkg -L perl`**</mark> command:

```
sysadmin@localhost:~$ dpkg -L perl
/.
/etc
/etc/perl
/etc/perl/CPAN
/etc/perl/Net
/etc/perl/Net/libnet.cfg
/usr
```

If you know the pathname for a file, then it is possible to determine which package was responsible for providing that file by using the <mark style="color:red;">**`-S`**</mark>option with the **`dpkg`** command.

For example, to discover the package that provide the `/usr/bin/perldoc` file, execute the following command:

```bash
sysadmin@localhost:~$ dpkg -S /usr/bin/perldoc
perl: /usr/bin/perldoc
```

By using the **`dpkg`** command with the <mark style="color:red;">**`-s`**</mark> option and a package name, **the following package information will be displayed:**

* Status
* Size
* Organization that mantains the package
* Dependencies
* Description

### Configuration Packages with `dpkg-reconfigure`

If the package needs to be reconfigured at some later point, execute the <mark style="color:red;">**`dpkg-reconfigure`**</mark> command.

```
dpkg-reconfigure [OPTIONS...] PACKAGES...
```

A good example of using the `dpkg-reconfigure` command is with the **`tzdata`** package. When this package is configured, the administrator sets the time zone information for the computer's current location. If a system is moved to another location, particularly in a different time zone, then this package should be reconfigured by executing the `dpkg-reconfigure tzdata` command.

### Searching for packages with `apt-cache`

If the system has the appropriate repositories configured in the `/etc/apt/sources.list` file and the administrator has already refreshed the cached information about which packages are available by executing the `apt-get update` command, then any user can search for packages by using the <mark style="color:red;">**`apt-cache search`**</mark> command:

```
apt-cache search KEYWORD
```

The `apt-cache` command can also search for packages in a different way. To find out which dependencies a package has, the <mark style="color:red;">**`apt-cache depends PACKAGE`**</mark> command will list the packages required by the package-name.

```bash
 $ apt-cache depends wfuzz
wfuzz
  Depende: python3-pycurl
  Depende: python3-pyparsing
  Depende: python3-chardet
  Depende: python3-future
  Depende: python3-six
  Depende: <python3:any>
    python3:i386
    python3

```

The <mark style="color:red;">**`apt-cache show PACKAGE`**</mark> command can also display the status of a package with information similar to the output of the `dpkg -s PACKAGE` command, but the `apt-cache` command displays more detailed information.

### Installing/Updating Packages with `apt-get`

After searching for a package, you may want to install that package and its required dependencies. Using the <mark style="color:red;">**`apt-get install PACKAGE`**</mark> command will download and prompt the administrator to install that package as well as its dependencies.&#x20;

For example, to install the `apache2` package and its dependencies, execute the `apt-get install apache2` command with administrative privileges:

{% hint style="info" %}
If the package is installed and a newer version is available, execute the <mark style="color:red;">**`apt-get -only-upgrade install PACKAGE`**</mark> command

```
sysadmin@localhost:~$ sudo apt-get --only-upgrade install apache2        
```
{% endhint %}

To update all packages of the system, two commands are executed. First, the <mark style="color:red;">**`apt-get update`**</mark> command to **update the cache of all packages available** should be executed. Second, execute the <mark style="color:red;">**`apt-get upgrade`**</mark> command; **all packages and dependencies will be updated**

### Removing/Purging Packages with `apt-get`

Just as the `dpkg` command is able to either remove or purge a package, so can the `apt-get` command.

{% hint style="warning" %}
**Recall that the difference between the two is that purging deletes all packages files, whiel removing deletes all but the configuration files for the packages.**
{% endhint %}

An administrator can execute the <mark style="color:red;">**`apt-get remove PACKAGE`**</mark> command to remove a package or the <mark style="color:red;">**`apt-get purge PACKAGE`**</mark> command to purge a package completely from the system.

## Verifying Files with Checksums

A checksum is a small piece of complementary data used to verify the integrity of a file. If checksums do not mach, the copied file is corrupted and untrustworthy.

{% hint style="danger" %}
A checksum verifies the integrity of the downloaded file, but does not verify the authenticity of the sender.
{% endhint %}

### md5sum

The <mark style="color:red;">**`md5sum`**</mark> command, based on the MD5 algorithm, creates a 128 bit hash using the original file.&#x20;

```
md5sum [OPTIONS]... [FILE]...
```

The `md5sum` can also be used to authenticate the file with the check `-c` option. This is used to verify the integrity of the file by comparing the computed checksum of the file with the previously generated

### sha256sum

The <mark style="color:red;">**`sha256sum`**</mark> comands creates a 256-bit cheksum number that can be used to verify a file.&#x20;

```
sha256sum [OPTIONS]... [FILE]...
```

### sha512sum

The <mark style="color:red;">**`sha512sum`**</mark> command creates an even more secur 512-bit checksum for verifying files using the similar syntax.

```
sha516sum [OPTIONS]... [FILE]...
```

## SUSE Package Management

Several distributions use the **ZYpp/libzypp** package management engine, which primarly runs on openSUSE, SUSE Linux Enterprise, and Ark.  Lipzipp was designed to perform as the software package management scheme for SUSE products as well as the failed and now mostly dormant Zenworks Linux management suite from Novell.

While **ZYpp/libzypp** is the background package management engine, which is in charge of satisfying the dependencies between packages, most system operators will interact mainly with the <mark style="color:red;">**`zypper`**</mark> command line tool.

Repositories come in several varieties:

* The first being those that are official and supported, such as the subscription channels/repositories that the SCC (SUSE Customer Center) provides for those with support subscriptions.
* The next variety is a 3rd-party repository. An excellent example of this is the Packman repository for the openSUSE community.
* The last variety is the local mirror or synchronized repository that an organization will use to maintain a local copy of the software that can be used by their internal or public-facing systems.

The <mark style="color:red;">**`zypper`**</mark> command is used to query, install, remove, update, manage repositories, and more; **it depends on the **<mark style="color:red;">**`rpm`**</mark>** command's abilities** to accomplish most of these tasks.

Using `zypper` to install the same packages is much easier, as it will do the hard work of reading the desired package's dependencies, query the database of installed packages for those, and if they are not found, determine which software repository from the list of configured repositories that they may be obtained from.

The syntax used for the `zypper` command examples is the following:

```
zypper [--GLOBAL-OPTS] COMMAND [--COMMAND-OPTS] [COMMAND-ARGUMENTS]
```

### Searching for Packages with `zypper`

It is critical to refresh the information that <mark style="color:red;">**`zypper`**</mark> has about the repositories that the commands in this section will be querying and installing software from. The refresh <mark style="color:red;">**`ref`**</mark> command can be used to refresh the repository.

```bash
localhost:~ # zypper ref
Repository 'sle-module-basesystem' is up to date.                               Repository 'sle-module-desktop-applications' is up to date.                     Repository 'sle-module-development-tools' is up to date.                        Repository 'Non-OSS' is up to date.
Repository 'SLES15-15-0' is up to date.
Repository 'SLES' is up to date.
Repository 'sle-module-server-applications' is up to da the date. 
```

To **find a package** to install, use the search <mark style="color:red;">**`se`**</mark> command to query the configured repositories on the system

```
zypper se gvim
```

### Installing Packages with `zypper`

The **`zypper`** command can be used with the install <mark style="color:red;">**`in`**</mark> command to install packages.

```
zypper in PACKAGE
```

```
localhost:~ # zypper in gvim
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following NEW package is going to be installed:
  gvim

1 new package to install.
```

To reinstall (and force the overwriting of) the `gvim` package, as the root user, use the <mark style="color:red;">`-f`</mark> option to the `zypper` command:

```bash
localhost:~$ zypper in -f gvim
Loading repository data...
Reading installed packages...
Forcing installation of 'gvim-8.0.1568-3.20.x86_64' from repository 'sle-module-desktop-applications'.
Resolving package dependencies...

The following package is going to be reinstalled:
  gvim

1 package to reinstall.
Overall download size: 1.6 MiB. Already cached: 0 B. No additional space will be used or freed after the operation.
Continue? [y/n/...? shows all options] (y): y
```

### Managing repositories with `zypper`

To query the software repositories on a system, use the **`zypper`** command with the _**list repositories**_** **<mark style="color:red;">**`lr`**</mark> option:

```
localhost:~ # zypper lr
Repository priorities in effect:                                               (See 'zypper lr -P' for details)
      99 (default priority) :  6 repositories
     100 (lowered priority) :  1 repository

# | Alias                            | Name                            |Enabled | GPG Check | Refresh
--+----------------------------------+---------------------------------+---------+-----------+--------
1 | Basesystem-Module_15-0           | sle-module-basesystem           | Yes     | (r ) Yes  | No
2 | Desktop-Applications-Module_15-0 | sle-module-desktop-applications | Yes     | (r ) Yes  | No
3 | Development-Tools-Module_15-0    | sle-module-development-tools    | Yes     | (r ) Yes  | No
4 | Non-OSS                          | Non-OSS                         | Yes     | (r ) Yes  | No
5 | SLES15-15-0                      | SLES15-15-0                     | Yes     | (r ) Yes  | No
```

**To **_**add a repository**_** to install additional software** from, first find the repository URL, the use t repository <mark style="color:red;">**`-ar`**</mark> option:

```
localhost:~ # zypper ar -f http://packman.inode.at/suse/openSUSE_Leap_15.1/ packman
Adding repository 'packman'.............................................[done]
Repository 'packman' successfully added

URI         : http://packman.inode.at/suse/openSUSE_Leap_15.1/
```

At this time, the `zypper ref` command would be run again to update the system with the latest metadata from all repositories, which will prompt for trusting the repository key.

When the query of the new repository is successful, the system can be updated or software operations are undertaken.

```bash
localhost:~ # zypper list-updates -t package
retrieving repository 'packman' metadata -------------------------------------------------------------------[|]

New repository or package signing key received:

  Repository:       packman
  Key Name:         PackMan Project (signing key) <packman@links2linux.de>
  Key Fingerprint:  F8875B88 0D518B6B 8C530D13 45A1D067 1ABD1AFB
  Key Created:      Mon Sep 15 16:18:00 2014
  Key Expires:      Thu Sep 12 16:17:21 2024
  Rpm Name:         gpg-pubkey-1abd1afb-54176598
```

### Updating Packages with `zypper`

The `zypper` command can also be used to **update the packages in a repository**

```
zypper -list-updates PACKAGE
```

To see a listing of all the packages that are available for update from your repositories, you would use the <mark style="color:red;">**`list-updates`**</mark> command:

```
localhost:~ # zypper list-updates -t gvim
Loading repository data...
Reading installed packages...
No updates found.
```

To perform an update of the system, (which will locate all updates in the configured repositories and perform a dependency-solve on them) use the `update` command:

```
localhost:~ # zypper update
Loading repository data...
Reading installed packages...
No updates found.
```







