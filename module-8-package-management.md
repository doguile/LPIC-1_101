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

The `*` glob character may be useful if the exact package name is not known. For example, if you knew that the package name contained `python`, but you were unsure of the rest, then execute the following:

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

``
