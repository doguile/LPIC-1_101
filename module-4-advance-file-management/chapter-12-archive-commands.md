---
description: '103.3: Perform basic file management v2  Weight: 4'
---

# Chapter 12: Archive Commands

<details>

<summary>Key terms</summary>

<mark style="color:red;">`bunzip2`</mark> Command that uncompresses files compressed using the `bzip2` command and removed the `.bz2` extension

<mark style="color:red;">`bzcat`</mark> Command prints the content of specified files compressed with the **`bzip2`** command to the standard output

<mark style="color:red;">`gunzip`</mark> Command that decompress files created by `gzip`, `zip`, compress, compress -H or pack.

<mark style="color:red;">`gzip`</mark> Command used to compress or expand files. Gzip reduces the size of the named files using Lempel-Ziv coding (L277). Keep the same ownership modes, access and modification times.

<mark style="color:red;">`unxz`</mark> A method of uncompressing files compresed using the `xz` command.

<mark style="color:red;">`xz`</mark> Command that can compress or  decompress `.zx` or `lzma` files.&#x20;

<mark style="color:red;">`xzcat`</mark> Command used to print the contents of files compressed with the **`xz`** command to  standard output on the terminal **without uncompressing the target file**

<mark style="color:red;">`zcat`</mark> Command used to display the contents of a compressed file from a compressed archive created by the **`tar`** command or **`gzip`** command **without uncompressing it.**

</details>

Linux distribution provide several different sets of commands for _compression_ and _archiving_ files and directories.

File archiving is used when one or more files need to be transmitted or stored as efficiently as possible. There are two fundamentals aspects which this chapter explores:

* **Archiving**: Combines multiples files into one, which eliminates the overhead in individual files and make it easier to transmit.
* **Compression**: Makes the files smaller by removing redundant information.

{% hint style="warning" %}
Files can be compressed individually, or multiple files can be combined into a single one archive and then subsequently compressed.&#x20;
{% endhint %}

## Compression

## The `gzip` and `gunzip` commands

_<mark style="background-color:red;">**Compression**</mark>_ <mark style="background-color:red;"></mark><mark style="background-color:red;"></mark> <mark style="background-color:red;"></mark><mark style="background-color:red;">**reduces the amount of data**</mark> needed to store or transmit a file while storing it in such a way that the file can be restored. The compressed version of the file is not typically viewed or utilized; instead, it is uncompressed before use.

The <mark style="color:red;">**`gzip`**</mark> command is used to **create a compressed file**. Likewise, the <mark style="color:red;">**`gunzip`**</mark> command is used to view the contents of a compressed file, as well as extract those contents.

```
gzip [OPTION]... [FILE]...
gunzip [OPTION]... [FILE]...
```

In the following example, the `longfile.txt` file is replaced with the compressed `longfile.txt.gz` file after using the <mark style="color:red;">`gzip`</mark> command:

```bash
sysadmin@localhost:~$ cd Documents/
sysadmin@localhost:~/Documents$ ls longfile.txt
longfile.txt
sysadmin@localhost:~/Documents$ gzip longfile.txt
sysadmin@localhost:~/Documents$ ls longfile*
longfile.txt.gz
```

{% hint style="warning" %}
The <mark style="color:red;">`gzip`</mark> command should be used with caution, since the default behaviour is to **replace the original file** specified with a compressed version.
{% endhint %}

{% hint style="info" %}
The <mark style="color:red;">**`gzip`**</mark> command uses the <mark style="background-color:blue;">**Lempel-Ziv**</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;"></mark> data compression algorithm
{% endhint %}

**To avoid replacing the original version of a file when using the **<mark style="color:red;">**`gzip`**</mark>** command, use the **<mark style="color:red;">**`-c`**</mark>** option**. This cause the <mark style="color:red;">`gzip`</mark> command to send the compressed data to standard output, and given that the output of the <mark style="color:red;">`gzip`</mark> command is binary data, it will need to be redirected into a file.

Capture the output of the <mark style="color:red;">`gzip`</mark> command and **redirect it into a file**, using the greater-than <mark style="color:red;">`>`</mark> character.

```
gzip -c file.txt > file.gz
```

This can be useful as the gzipped file can be moved to an archive directory location while preserving the original file in its original location.

The <mark style="color:red;">**`gunzip`**</mark> command reverses what <mark style="color:red;">`gzip`</mark> does, so files will be uncompressed, and the gzipped file will be replaced with the uncompressed file.

```
sysadmin@localhost:~/Documents$ gunzip red.txt.gz
sysadmin@localhost:~/Documents$ ls red*
red.txt
```

**To list the amount of compression** of an existing file, use the <mark style="color:red;">`-l`</mark> option, with the <mark style="color:red;">`gunzip`</mark> command:

```bash
sysadmin@localhost:~/Documents$ gunzip -l numbers.txt.gz
         compressed        uncompressed  ratio uncompressed_name
                 42                  10 -20.0% numbers.txt
```

While the <mark style="color:red;">**`gzip`**</mark>** command supports **_**recursion**_** with the **<mark style="color:red;">**`-r`**</mark>** option**, by default, it attempts to replace the original file with the gzipped file, but, in order to be able to compress files with the <mark style="color:red;">`gzip`</mark> command recursively, **a user needs to have the rights permissions on the directories the files are in**.  Typically, this is limited to directories within the user's own home directory.

{% hint style="info" %}
To <mark style="color:red;">`gzip`</mark> and <mark style="color:red;">`gunzip`</mark> a file within a directory, **a user must have the **<mark style="color:red;">**write**</mark>** and **<mark style="color:red;">**execute**</mark>** permissions on a directory** as well as the <mark style="color:green;">**read**</mark>** permission on the file**.&#x20;
{% endhint %}

## The `zcat` command

The <mark style="color:red;">**`zcat`**</mark> command is <mark style="background-color:red;"><mark style="color:red;">****<mark style="color:red;"></mark><mark style="background-color:red;">** **</mark><mark style="background-color:red;">**used to display the contents of a**</mark><mark style="background-color:red;">** **</mark>_<mark style="background-color:red;">**compressed**</mark>_<mark style="background-color:red;">** **</mark><mark style="background-color:red;">**file without actually uncompressing it**</mark><mark style="background-color:red;">.</mark> It does this by expanding the file contents to standard output rather than creating a new uncompressed file from a compressed archive.&#x20;

Its effect is identical to running the <mark style="color:red;">`gunzip -c`</mark> command.

```bash
sysadmin@localhost:~/Documents/Work$ ls                                         
1.gz  2.gz  3.gz  4.gz  5.gz  6.gz  7.gz
                                        
sysadmin@localhost:~/Documents/Work$ gunzip -c 1.gz                             
hola coo estaas                                                                 

sysadmin@localhost:~/Documents/Work$ ls                                         
1.gz  2.gz  3.gz  4.gz  5.gz  6.gz  7.gz
```

```
zcat [OPTION]... [FILE]...
```

```bash
sysadmin@localhost:~/Documents$ gzip red.txt
sysadmin@localhost:~/Documents$ ls
School            alpha-third.txt  hidden.txt    numbers.txt  spelling.txt
Work              alpha.txt        letters.txt   os.csv       words
adjectives.txt    animals.txt      linux.txt     people.csv
alpha-first.txt   food.txt         longfile.txt  profile.txt
alpha-second.txt  hello.sh         newhome.txt   red.txt.gz
sysadmin@localhost:~/Documents$ zcat red.txt.gz
red
reef
rot
reeed
rd
rod
roof
reed
root
reel
read
```

The <mark style="color:red;">`zcat`</mark> command can also be used to view the contents of multiple compressed files:

```
sysadmin@localhost:~/Documents$ zcat red.txt.gz letters.txt.gz
```

Using the <mark style="color:red;">`-f`</mark> option with the <mark style="color:red;">`zcat`</mark> command will **enable viewing of normal uncompressed files.**

```
sysadmin@localhost:~/Documents$ zcat -f newhome.txt
```

**Files properties** can be displayed with the <mark style="color:red;">`-l`</mark> option:

```
sysadmin@localhost:~/Documents$ zcat -l red.txt.gz
         compressed        uncompressed  ratio uncompressed_name
                 60                  51  33.3% red.txt
```

## The `bzip2` and `bunzip2` Commands

The <mark style="color:red;">`bzip2`</mark> and <mark style="color:red;">`bunzip2`</mark> commands work in a nearly identical to the `gzip` and `gunzip` commands.

The <mark style="color:red;">`bzip2`</mark> utilities use a different **compression** algorithm called _ **Burrows-Wheeler** _ block sorting, that **can compress files smaller** than <mark style="color:red;">`gzip`</mark> at the **expense of more CPU time.**&#x20;

{% hint style="info" %}
These files can be recognized because they have a <mark style="color:red;">`.bz`</mark> or <mark style="color:red;">`.bz2`</mark> extension instead of a `.gz` extension&#x20;
{% endhint %}

```
bzip2 [OPTION]... [FILE]...
bunzip2 [OPTION]... [FILE]...
```

Using the verbose <mark style="color:red;">`-v`</mark> option will cause <mark style="color:red;">`bzip2`</mark> to report the compression ratio after it has finished.&#x20;

{% hint style="info" %}
The <mark style="color:red;">`gzip`</mark> command also supports the <mark style="color:red;">`-v`</mark> option.
{% endhint %}

{% hint style="danger" %}
The <mark style="color:red;">`bzip2`</mark> command should be used with causion since its default behaviour is to replace the original file specified with a compression version
{% endhint %}

The <mark style="color:red;">`bzip2`</mark> command **doesn't have an option **<mark style="color:red;">**`-r`**</mark>** to do recursion**, so a wildcard can be used to match multiple files.&#x20;

The <mark style="color:red;">`bzip2`</mark> command **does have the **<mark style="color:red;">**`-c`**</mark>** option available** to send the data to standard output, so it can redirected to a new file:

```bash
sysadmin@localhost:~/Documents$ bzip2 -cv alpha.txt > alpha.txt.bz2
  alpha.txt:  1.781:1,  4.492 bits/byte, 43.85% saved, 390 in, 219 out.
```

## The `bzcat` Command

The <mark style="color:red;">`bzcat`</mark> command **prints the content of specified files compressed with the **<mark style="color:red;">**`bzip2`**</mark> command to the standard output:

```
bzcat [OPTION]... [FILE]...
```

```
sysadmin@localhost:~/Documents$ bzcat profile.txt.bz2
Hello my name is Joe.
I am 37 years old.
3121991
My favorite food is avocados.
I have 2 dogs.
123456789101112
```

## The `xz` and `unxz` Commands

Another archival tool similar to `gzip` and `bzip2` is the <mark style="color:red;">`xz`</mark> commands.

```
xz [OPTION]... [FILE]...
```

To **compress a group a files individually, use the **<mark style="color:red;">**`xz`**</mark>** command with the **<mark style="color:red;">**`-z`**</mark>** option**. Compression is the default operation mode , and therefore if the `xz` command is run with no options, the `-z` option is implied.

```bash
sysadmin@localhost:~/Documents$ xz -z words                                     
sysadmin@localhost:~/Documents$ ls -l words.xz                                  
-rw-r--r-- 1 sysadmin sysadmin 198756 Apr 24  2019 words.xz                     
```

The <mark style="color:red;">**`-d`**</mark> option can be used with the <mark style="color:red;">`xz`</mark> command to **uncompress the files**:

```
sysadmin@localhost:~/Documents$ xz -d words
```

**Another method is to use the **<mark style="color:red;">**`unxz`**</mark>** command**. When a file is uncompressed using the `unxz` command, the extension **`.xz`** is removed.&#x20;

```
sysadmin@localhost:~/Documents$ unxz words.xz
sysadmin@localhost:~/Documents$ ls -l words
-rw-r--r-- 1 sysadmin sysadmin 971578 Dec  9 23:35 words
```

{% hint style="info" %}
Keep in mind when using the <mark style="color:red;">`xz`</mark> that the more aggressive the compression, the harder the processor will have to work.
{% endhint %}

## The `xzcat` Command

The <mark style="color:red;">`xzcat`</mark> command is used to **print the contents of files compressed with the **<mark style="color:red;">**`xz`**</mark> command to standard output on the terminal **without uncompressing the target file**.

```
xzcat [FILE]...
```

```
sysadmin@localhost:~/Documents$ xzcat animals.txt.xz
1 retriever
2 badger
3 bat
4 wolf
5 eagle
```

## _Archiving_

## The `tar` Command

**An archive is a single file, which consists of many files**, though **not necessarily compressed**. These tar archive files, sometimes called _tarballs_, were originally used to backup data onto magnetic tape.&#x20;

Tar is derived from the words "tape archive".

While the primary purpose of the <mark style="color:red;">`tar`</mark> command is to **merge multiple files into a single file**, it is capable of many differents operations. The functionality of the <mark style="color:red;">`tar`</mark> command can be broken down into 3 basics functions: **creating, viewing and extracting** archives.

* **Create**: Make a new archive out of series of files.
* **Extract**: Pull one or more files out of an archive.
* **List**: Show the contents of the archive without extracting.

The `tar` command accepts all three styles of options, (`x` `-x` `--extract`) as parameters. Regarding to no hyphens (BSD style), a single hyphen (UNIX style) or two hyphens (GNU style).

### Create Mode

```
tar -c [-f ARCHIVE] [OPTIONS] [FILE...]
```

Creating an archive with the <mark style="color:red;">`tar`</mark> command requires two named options:

* <mark style="color:red;">`-c`</mark> Create archive
* <mark style="color:red;">`-f`</mark> Use the ARCHIVE file. The argument ARCHIVE will be the name of the resulting archive file

All the remaining arguments, \[ FILE... ] are considered as input file names either as a list of files, as a wildcard, or both.

To create an archive of the Documents directory, use the <mark style="color:red;">`-c`</mark> option and file <mark style="color:red;">`-f`</mark> option to specify a new file, folllowed by the directory to archive.

{% hint style="danger" %}
Note that hte <mark style="color:red;">`-f`</mark> options must be specified last since it is indicating a file name.
{% endhint %}

```bash
sysadmin@localhost:~$ tar -cf mydocuments.tar ~/Documents
tar: Removing leading `/' from member names
sysadmin@localhost:~$ ls
Desktop    Downloads  Pictures  Templates  mydocuments.tar
Documents  Music      Public    Videos
```

The verbose <mark style="color:red;">`-v`</mark> option will cause the <mark style="color:red;">`tar`</mark> command to display the files that are being included in the archive. If compression is going to used, then the appropiate option needs to be specified. **For the **<mark style="color:red;">**`gzip`**</mark>** command, the **<mark style="color:red;">**`-z`**</mark>** option needs to be added.**

| `-v` | Verbosely list the files processed.                            |
| ---- | -------------------------------------------------------------- |
| `-z` | Compress (or decompress) the archive using the `gzip` command. |

Files extensions are not relevant to Linux; however, it is customary to add `.tar.gz` to the name of the compressed archive.&#x20;

```bash
sysadmin@localhost:~$ tar -cvzf mydocuments.tar.gz ~/Documents
sysadmin@localhost:~$ ls
Desktop    Downloads  Pictures  Templates  mydocuments.tar
Documents  Music      Public    Videos     mydocuments.tar.gz
```

It is often preferable to archive files first before zipping them in any format, whether `bzip`, `gzip`, or `xz`.&#x20;

```bash
sysadmin@localhost:~$ mkdir test

sysadmin@localhost:~$ tar -cf testdirectory.tar test

sysadmin@localhost:~$ ls
Desktop    Music     Templates             mydocuments.tar
Documents  Pictures  Videos                test
Downloads  Public    mydocuments.tar.gz  testdirectory.tar

sysadmin@localhost:~$ xz -z testdirectory.tar
sysadmin@localhost:~$ ls
Desktop    Music     Templates             
Documents  Pictures  Videos                test
Downloads  Public       testdirectory.tar.xz
```

To compress (or decompress) an archive with the <mark style="color:red;">`tar`</mark> command, choose the appropiate option:

* <mark style="color:red;">`-z`</mark> Filter the archive through the `gzip` command
* <mark style="color:red;">`-j`</mark> Filter the archive through the `bzip2` command.
* <mark style="color:red;">`-J`</mark> Filter the archive through the `xz` command.

```
sysadmin@localhost:~$ tar -cJf testdirectory.tar.xz test
sysadmin@localhost:~$ tar -czf testdirectory.tar.gzip test
sysadmin@localhost:~$ tar -cjf testdirectory.tar.bzip2 test
```

### List Mode

```
tar -t [-f ARCHIVE] [OPTIONS]
```

Use the <mark style="color:red;">**`-t`**</mark> option to the <mark style="color:red;">`tar`</mark> command to **view a list of a tar file**.

* <mark style="color:red;">**`-t`**</mark> List the files in the archive
* `-f` `ARCHIVE` Operate on the given archive

Even if the archive file is compressed, **the correct compression option does not need to be specified to view a tar achive file**. Only the <mark style="color:red;">`-f`</mark> option and the list <mark style="color:red;">`-t`</mark> option are required to view the table of contents.

```
sysadmin@localhost:~$ tar -tf mydocuments.tar.gz
home/sysadmin/Documents/
home/sysadmin/Documents/animals.txt
home/sysadmin/Documents/alpha-third.txt

sysadmin@localhost:~$ tar -vtf mydocuments.tar.gz
-drwxr-xr-x sysadmin/sysadmin 0 2019-04-24 16:24 home/sysadmin/Documents/
```

### Extract Mode

```
tar -x [-f ARCHIVE] [OPTIONS]
```

To extract the files from the tar file, use the <mark style="color:red;">**`-x`**</mark> option:

* <mark style="color:red;">**`-x`**</mark> Extract files from the archive
* <mark style="color:red;">**`-f`**</mark> Operate on the given archive

## The `zip` and `unzip` Commands

The functionality of <mark style="color:red;">**`zip`**</mark> compressed file managment has been available in the personal compunting world prior to Windows.

The <mark style="color:red;">`zip`</mark> command is very useful for creating archives that can easily be shared across multiple operating systems. The basic form of a `zip` command is:

```
zip [OPTIONS]... [FILE]...
```

The **destination file will automatically have a **<mark style="color:red;">**`.zip`**</mark> extension added to it if an extension is not specified. Also, **the original files will not be replaced**.

```bash
sysadmin@localhost:~/Documents$ cd
sysadmin@localhost:~$ zip ./example/package ./example/*
  adding: example/one (stored 0%)
  adding: example/three (stored 0%)
  adding: example/two (stored 0%)
sysadmin@localhost:~$ ls ./example/
one  package.zip  three  two
```

One especially useful option for the `zip` command is the <mark style="color:red;">**`-r`**</mark> option, which allows the `zip` command to **recursively compress multiple directories** into a single file.

```
sysadmin@localhost:~$ zip -r ./example/logfiles /var/log/apt/
  adding: var/log/apt/ (stored 0%)
  adding: var/log/apt/eipp.log.xz (stored 0%)
  adding: var/log/apt/history.log (deflated 77%)
  adding: var/log/apt/term.log (deflated 91%)
sysadmin@localhost:~$ ls ./example/
logfiles.zip  one  package.zip  three  two
```

The <mark style="color:red;">**`unzip`**</mark> command is used to extract the files from the zip archive file.

```bash
sysadmin@localhost:~/example$ unzip ./logfiles.zip
Archive:  ./logfiles.zip
   creating: var/log/journal/
   creating: var/log/apt/
 extracting: var/log/apt/eipp.log.xz
  inflating: var/log/apt/history.log
  inflating: var/log/apt/term.log
sysadmin@localhost:~/example$ ls ~/example/var/log/apt
eipp.log.xz  history.log  term.log
```

To view the contents of a zip file without unpacking it, use the `unzip` command with the list `-l` option:

```bash
sysadmin@localhost:~/example$ unzip -l ./package.zip
Archive:  ./package.zip
        Archive:  ./package.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2019-03-22 00:14   example/one
        0  2019-03-22 00:14   example/three
        0  2019-03-22 00:14   example/two
---------                     -------
        0                     3 files
```

## The `cpio` Command

Another type of **archive command** that can merge many files into a single file is the <mark style="color:red;">**`cpio`**</mark> command. This command gets its name from two of its mode: _copy-in_ mode and _copy-out_ mode.&#x20;

* In _**copy-out**_ mode, the <mark style="color:red;">**`cpio`**</mark> command will <mark style="background-color:red;">copy files from a directory into an archive</mark>.&#x20;
* In ** **_**copy-in**_ mode, the <mark style="color:red;">**`cpio`**</mark> command will <mark style="background-color:purple;">either list the archive file contents or copy files from the archive into a directory</mark>.&#x20;

> Just remember that the archive is "outside" of the OS

There is a third mode called the ** **_**copy-pass**_** ** mode. In this mode, the <mark style="color:red;">**`cpio`**</mark> command <mark style="background-color:red;">copies files from one directory to another</mark>, which combines the copy-out and copy-in modes without creating an archive file.

To create a new archive file, the <mark style="color:red;">**`cpio`**</mark> command will run in copy-out mode taking a list of files from _stdin_ and producing a file stream that can be redirected into a new archive file.

### Copy-Out Mode

**The **<mark style="color:red;">**`-o`**</mark>** option puts the **<mark style="color:red;">**`cpio`**</mark>** command into copy-out mode**. Using the verbose <mark style="color:red;">**`-v`**</mark> option will cause the <mark style="color:red;">`cpio`</mark> command to list the files that it processes. So, to archive the current directory, execute the `ls` command, and then send a list of the files into the <mark style="color:red;">`cpio`</mark> command as input by using the pipe `|` character. The `>` character will capture the output of the <mark style="color:red;">`cpio`</mark> command and put it into a file:

```bash
sysadmin@localhost:~$ ls | cpio -ov > archive.cpio
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
example
1 block
```

For example, to archive the home directory and all of its subdirectories and files, execute the following command

```
sysadmin@localhost:~$ find . -depth -print | cpio -vo > /tmp/ar.cpio
./Public
./Downloads
./Pictures
./Videos
./.selected_editor
```

### Copy-in Mode

To extract the files that are in a <mark style="color:red;">`cpio`</mark> archive, **use the **<mark style="color:red;">**`-i`**</mark>** option** with the <mark style="color:red;">`cpio`</mark> command to **specify copy-in mode.**&#x20;

{% hint style="info" %}
By default, <mark style="color:red;">`cpio`</mark> will **** not overwrite **** existing files, to **overwrite use  the **<mark style="color:red;">**`-u`**</mark>** option** is used. The <mark style="color:red;">`cpio`</mark> command will not **create directories** unless the <mark style="color:red;">**`-d`**</mark>** option is used**
{% endhint %}

The <mark style="color:red;">`cpio`</mark> command also makes **use of `stdin`** to determine the name of the file that will be extracted from the archive.&#x20;

```
sysadmin@localhost:~$ cpio -idv /tmp/test < /tmp/ar.cpio
2048 blocks
```

To specify the **pass-through(transferencia)** mode of the <mark style="color:red;">`cpio`</mark> command, **specify the **<mark style="color:red;">**`-p`**</mark>** option**. If any directories are included, the <mark style="color:red;">`-d`</mark> option needs to be specified.

```
sysadmin@localhost:~$ find ~ | cpio -pd /tmp/destination
2044 blocks
```

&#x20;To prevent problems with files that have white spaces characters embedded in them, specify the <mark style="color:red;">`-pritnt0`</mark> option to the <mark style="color:red;">`find`</mark> command.&#x20;

For the <mark style="color:red;">`cpio`</mark> command to process the list of null separated files, add the <mark style="color:red;">`--null`</mark> option.&#x20;

```
sysadmin@localhost:~$ find . -print0 | cpio --null -pvd /tmp/destination
```

{% hint style="danger" %}
**Consider this**

The <mark style="color:red;">`cpio`</mark> command **automatically preserves file attributes (metadata)** like links, permissions, timestamps, and ownerships. These attributes are not preserved with the <mark style="color:red;">`cp`</mark> command.

The <mark style="color:red;">`cpio`</mark> command also works with special files better than the `cp` command.
{% endhint %}

## The `dd` Command

The `dd` command is a utility for copying files or entire partitions at the bit level. This command has several useful features, including:

* It can be used to clone or deleted entire disks or partitions
* It can be used to copy raw data to removable devices , such as USB drives and CDROMs
* It can be backup and restore the MBR
* It can be used to create a file of a specific size that is filled with binary zeros, which can then be used as a swap file

The `dd` command uses special arguments to specify how it will work.

|                 |                                                                                                                                                  |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `if=FILE`       | The input file to be read                                                                                                                        |
| `of=FILE`       | The output file to be written                                                                                                                    |
| `bs=size`       | The block size to be used. By default the value is to be considered to be in bytes. Use the following suffixes to specify `K`, `M` ,`G`, and `T` |
| `count=NUMBER`  | The number of block to read from the input file.                                                                                                 |

In the following example, a file named `/tmp/swapex` is created with 500 "one-megabyte" size blocks of zeros:

```bash
dd if=/dev/zero of=/tmp/swapex bs=1M count=500
500+0 records in
500+0 records out
524288000 bytes (524 MB) copied, 0.825745 s, 635 MB/s
```

No block size or count needs to be specified when copying over entire devices. For example, **to clone from one hard drive `/dev/sda` to another `/dev/sdb`**, execute the following command:

```
sysadmin@localhost:~$ dd if=/dev/sda of=/dev/sdb
```

The `dd` command can even be used to make an `.iso` image backup of your CDROM or DVD device. The following will take all of the data from the DVD `/dev/dvd` and stores the data into a local file called `dvd.iso`:

```
dd if=/dev/dvd of=dvd.iso
```
