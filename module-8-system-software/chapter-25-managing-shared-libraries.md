---
description: '102.3 : Manage shared libraries v2  Weight: 1'
---

# Chapter 25: Managing Shared Libraries

<details>

<summary>Key terms</summary>

`/etc/ld.so.conf` One of the cache files used by ldconfig

`LD_LIBRARY_PATH` A colon-separated set of directories where libraries should be searched for first, before the standard set of directories.

`ldconfig` Creates the necessary links and cache to the most recent shared libraries and checks the header and filenames of the libraries it encounters when determining which versions should have their links updated.

`ldd` Prints the shared libraries required by each program or shared libraries specified on the command line

</details>

Shared libraries, also known as **shared objects or system libraries**, are files that include the **`.so`** **extension as part of their name**.

When a program is executed, the <mark style="color:red;">**`/lib/ld-linux.so`**</mark>** dynamic linker will find and load the shared libraries needed by a program**, prepare the program to execute, and then run it. Older binaries in the `a.out` format are linked and loaded by the `/lib/ld.so` program.

{% hint style="info" %}
Both programs will search for the libraries in the **`/lib`** directory, the **`/usr/lib`** directory, the directories listed in the **`LD_LIBRARY_PATH`** environment variable, and from the **`/etc/ld.so.cache`** cache file.
{% endhint %}

Library files that are located in the **`/lib`** and **`/usr/lib`** directories are normally configured to be automatically found by the programs that needed them, but placing library files in other locations, such as the **`/usr/local/lib`** directory, may require configuring the system to be able to locate the shared library when needed.

## `/etc/ld.so.conf` File

The **`/etc/ld.so.conf`** file is **used to configure which directories are searched for library files** by the <mark style="color:red;">**`ldconfig`**</mark> command during the boot process or when executed by the administrator.

The <mark style="color:red;">**`ldconfig`**</mark> command **creates links and caches the most recent shared libraries** that are required by programs installed on the systems.&#x20;

The `/etc/ld.so.conf` file contains the following content:

```
include ld.so.conf.d/*conf
```

This modular approach allows for packages that might be installed to add their own `.conf` file to the `/etc/ld.so.conf.d` directory.

## Manually adding Library Files

If an administrator is compiling software from the source or using software that is not packaged, a `.conf` file needs to be manually created.

For example, the administrator may have downloaded and installed software that was not packaged in an **`.rpm`** or **`.deb`** file and then installed it in directories under the `/usr/local` directory structure, **with its library files located under the `/usr/local/lib`** directory structure. In order for these library files to be able to be loaded, **create a **<mark style="color:red;">**`/etc/ld.so.conf/local.conf`**</mark> file with the following content:

```
/usr/local/lib
```

After adding or removing files in the `/etc/ld.so.conf.d` directory, the administrator needs to **execute the **<mark style="color:red;">**`ldconfig`**</mark>** command to update the **<mark style="color:red;">**`/etc/ld.so.cache`**</mark>** cache file**.

After updating the `/etc/ld.so.cache` file, the <mark style="color:red;">`ldconfig`</mark> command can be used to display useful information about the system's libraries.

To **display the name and path information for all the libraries** that have been added to the cache use the <mark style="color:red;">**`ldconfig`**</mark> command with the <mark style="color:red;">**`-p`**</mark> option:

```bash
sysadmin@localhost:~$ ldconfig -p | head -n 20
799 libs found in cache `/etc/ld.so.cache'
	libzypp.so.1702 (libc6,x86-64) => /usr/lib64/libzypp.so.1702
	libzio.so.1 (libc6,x86-64) => /usr/lib64/libzio.so.1
	libz.so.1 (libc6,x86-64) => /lib64/libz.so.1
	liby2util.so.5 (libc6,x86-64) => /usr/lib64/liby2util.so.5
	liby2.so.4 (libc6,x86-64) => /usr/lib64/liby2.so.4
	libyui.so.8 (libc6,x86-64) => /usr/lib64/libyui.so.8
	libyelp.so.0 (libc6,x86-64) => /usr/lib64/libyelp.so.0
	libycpvalues.so.6 (libc6,x86-64) => /usr/lib64/libycpvalues.so.6
	libycp.so.5 (libc6,x86-64) => /usr/lib64/libycp.so.5
	libyaml-0.so.2 (libc6,x86-64) => /usr/lib64/libyaml-0.so.2
	libx86emu.so.1 (libc6,x86-64) => /usr/lib64/libx86emu.so.1
```

To display the **list of libraries directories that are configured as well as their contents**, the <mark style="color:red;">**`-v`**</mark> **option** can be used:

```bash
sysadmin@localhost:~$ ldconfig -v | head -n 20
/usr/local/lib64:
/usr/local/lib:
/usr/lib64/graphviz:
	libgvplugin_neato_layout.so.6 -> libgvplugin_neato_layout.so.6.0.0
	libgvplugin_dot_layout.so.6 -> libgvplugin_dot_layout.so.6.0.0
	libgvplugin_core.so.6 -> libgvplugin_core.so.6.0.0
/lib:
```

## `LD_LIBRARY_PATH`

Users without administrative access to the system can also configure directories that will be searched by setting the `LD_LIBRARY_PATH` environment variable with the list of library directories. For example, if the user `jose` installed a program in the `/home/jose/app` directory with the library files for that application in the `/home/jose/app/lib` directory, then that user could execute:

```
sysadmin@localhost:~$ export LD_LIBRARY_PATH=/home/jose/app/lib
```

Once that line has been executed, then the library files found in the `/home/jose/app/lib` directory could be found and located.

## `ldd` Command

To **verify or view the library files associted with a program**, use the <mark style="color:red;">**`ldd`**</mark> command.&#x20;

```
sysadmin@localhost:~$ 1dd /bin/bash
        linux-vdso.so.1 =>  (0x00007fffe7ffe000)
        libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5
(0x00007f280231e000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f280211a000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f2801d53000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f280254d000)
```

If there is a problem with a library file not being loaded, then the line of the output may report `not found`.&#x20;

```bash
sysadmin@localhost:~ 1dd /usr/bin/mysq1
ldd: /usr/bin/mysql: No such file or directory
```
