---
description: '107.3: Localisation and internationalisation v2  Weight: 3'
---

# Chapter 8: Localization

<details>

<summary>Key terms</summary>

`/etc/localtime` File used to configure the time zone of the system. To setup a particular time zone, a symbolic link is created from the /etc/localtime to the corresponding file in the /usr/share/zoneinfo directory

`/etc/timezone` File that stores the system timezone

`/usr/share/zoneinfo/` The time zone data for different regions is maintained in this directory.

`ASCII` American Standard Code for Information Interchange. ASCII is a 7-bit encoding scheme, used to encode letters, numerals, symbols and device control codes as fiexd-length codes.

`ISO-8859` ISO/IEC 8859 is a joint International Organization for Standardization (ISO) and Internation Electrotechnical Commission (IEC) series of standards for 8-bit character encoding

`LANG` If set, all locale environmental variables are set to the values of LANG

`LC_*` If set, all locale environmental variables are set to the value of LC\_\*

`LC_ALL` If set, all locale environmental variables are set to the value of LC\_ALL

`TZ` Environment variable is used to determine the timezone and how to calculate local time

`UTF-8` UCS Transformation Format - 8 bit. UCS is Universal Character Set upon which many character encondings are based.

`Unicode` Universal Character Set (UCS), it is a single character set whose goal is to be superset of all other prior sets and to contain every character used in writing any language.

`date` Command used to display or set the current date and time.

`iconv` Command used to convert one character enconding to another. The iconv command is suitable to convert characters in a large number of files

`locale` The locale command without any arguments gives the summary of each locale category

`tzselect` Command used to determine the timezone value used to set the TZ variable for a particular location by allowing the user to choose the country and location from a set of menus

</details>

## Introduction

The concept of localization is to make easy for the administrator or individual users to set and switch their working environment to match conventions specific to a certain language in a certain country.

## Locale

The term _locale_ referts to a **set of parameters that define the user's language, country, and any special variant preferences**. These parameters include the following:

* Language
* numeric representation
* Date-and-time representation
* Monetary units and symbols
* Case conversion - for proper case mapping of characters
* String collation - for determining sort order rules for a country
* Character clasification - determines the correct set of characters, digits, punctuation and symbols.

### Localization

To serve different cultures, a program should be able to determine its locale and act accordingly. Localization is the process of creating or adapting a product to be suitable for a specific group in terms of language, culture, and targeted needs.

There are two methods of providing locale information:

1. Locally-run programs use locale information provided by environment variables
2. Web-based applications use locale information either obtained from the web browser or explicity requested as a form value.

### Locale Naming Convention

Locale definition files are used to define the **language**, **territory**, and **code set** information applicable to the user. Locale definition files use the following naming convention:

```
language[_territory][.codeset][@modifiers]
```

The most common character code set used today is UTF-8 because it contains a universal set of characters plus supports Chinese, Japanese, and Korean double-width characters.

Additional locale definition file examples are shown below:

| Locale             | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| `en_AU.ISO-8859-1` | Language: English; Territory: Australia; Codeset: ISO-8859-1 |
| `hi_IN.UTF-8`      | Language: Hindi; Territory: India; Codeset: UTF-8            |

### Viewing the Current Locale

The <mark style="color:red;">**`locale`**</mark> **command** without any arguments gives the **summary of each locale category** ( `LC_x` ). The output from the command will appear similar to this:

```bash
sysadmin@localhost:~$ locale                                             
LANG=                                                                           
LANGUAGE=                                                                       
LC_CTYPE="POSIX"                                                                
LC_NUMERIC="POSIX"                                                              
LC_TIME="POSIX"                                                                 
LC_COLLATE="POSIX"                                                              
LC_MONETARY="POSIX"                                                             
LC_MESSAGES="POSIX"                                                             
LC_PAPER="POSIX"                                                                
LC_NAME="POSIX"                                                                 
LC_ADDRESS="POSIX"                                                              
LC_TELEPHONE="POSIX"                                                            
LC_MEASUREMENT="POSIX"                                                          
LC_IDENTIFICATION="POSIX"                                                       
LC_ALL=
```

Variables whose values are within quotation marks are not set explicity; they inherit their values from `LANG` or `LC_ALL` .Values without quotation marks have been set explicity.

If the `LC_ALL` locale variable is set to `en_US` locale, all locale environment variables are set to the `en_US` locale.

If **information about a specific locale category** is needed, the category <mark style="color:red;">**`-c`**</mark>** option** for the locale command can be used:

```bash
sysadmin@localhost:~$ locale -c LC_NAME
LC_NAME                                                                         
%p%t%g%t%m%t%f                                                                  
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
ANSI_X3.4-1968
```

Alternatively, the keyword <mark style="color:red;">**`-k`**</mark> option will **show you information about a specific keyword**. Keywords generally start with `LC_` or `LANG`

```
sysadmin@localhost:~$ locale -k LC_NAME
name_fmt="%p%t%g%t%m%t%f"                                                       
name_gen=""                                                                     
name_mr=""                                                                      
name_mrs=""                                                                     
name_miss=""                                                                    
name_ms=""                                                                      
name-codeset="ANSI_X3.4-1968"
```

### Listing All the Available Locales

To **display the list of locales that are variable** on the current system, based on the locale selected during installation, use the <mark style="color:red;">`locale`</mark> command with the _all_ <mark style="color:red;">**`-a`**</mark> option.

```bash
sysadmin@localhost:~$ locale -a                                             
C
C.UTF-8
en_AG
en_AG.utf8
en_AU.utf8
en_BW.utf8
en_CA.utf8
en_DK.utf8
en_GB.utf8
```

### Change System's Default `locale` Settings for all Users

To permanently change the system's locale, the following steps would need to be performed as root:

1. Edit the global locale settings file:
   1. <mark style="color:orange;">**`/etc/default/locale`**</mark> (Debian-based system)
   2. <mark style="color:orange;">**`/etc/sysconfig/i18n`**</mark> (Red Hat-based systems)
2. Change the `LANG` (language) variable inside the file to the desired value (from the list of available locales):

```
LANG="es_ES.utf8"
```

3\. After rebooting the system, the changes will take effect.

4\. Verify using the <mark style="color:red;">`locale`</mark> command.

### Change User Locale for Current Login Session

To change the language and encoding for the current login session, set the `LANG` environmental variable to equal one of the variable locales.&#x20;

| Locale       | LANG variable setting    |
| ------------ | ------------------------ |
| English (US) | `LANG=en_US.UTF-8`       |
| Russian      | `LANG=ru_RU.UTF-8`       |
| French       | `LANG=fr_FR.ISO-8859-15` |

### Customizing a User's Locale

If a user requires a different locale than the system default, place the following line in one of the bash initialization files (`~/.bashrc` or `~/.profile` )

```
export LANG=en_US.UTF-8
```

Changes will be effective only after the user logs out and logs back in.

### Role of LANG=C

**The **<mark style="color:red;">**`LANG`**</mark>** environment variable controls localization**. It is used to select how your computer treats language-specific features. In turn, it affects the behavior of command line tools like the `sort`, `grep` ,and `awk` commands.

There can be a situation when it is required to see warning and error messages in English alone.

Setting the `LANG` enviroment variable value to `C` tells all programs and tools to consider only basic ASCII characters (0-9, A-Z, special characters) and disable UTF-8 multibyte match. In a way, `LANG=C` disables localization.&#x20;

To set the `LANG` envioroment variable to `C`, execute the following command:

```
sysadmin@localhost:~$ export LANG=C
```

It is possible to use `LANG=C` temporarily. The following command temporarily overrides the language for one program, the `ls` command in this example, and display all output in English:

```bash
sysadmin@localhost:~$ LANG=C  ls  /noexist
ls:  cannot access  /noexist:  No such file or directory
```

The next command brings back the default behavior when the `LANG=C` is not used:

```bash
sysadmin@localhost:~$ ls  /noexist
ls:  cannot access  /noexist:  No such file or directory
ls:  kan  geen  toegang   krijgen  tot  /noexist:  Bestand  of  map  bestaat  niet
```

## Locale Environment Variables

When a program is executed, it receives information about the context in which it was invoked via two different methods. The first method uses the `argv` and `argc` arguments to its main function. The second method is the use of environment variables.&#x20;

The following chart describes environment variables that are typically used to modify locale settings:

| Variable      | Description                                                                                                                                                                                                                |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `LANG`        | <p>Specifies the default locale to use for attribute categories where neither LC_ALL nor the specific environment variable for that category is set. Example:</p><pre><code>LANG="en_US.UTF-8"</code></pre>                |
| `LC_ALL`      | If this environment variable is set, it overrides the selection for all the locales done using the other `LC_*` environment variables. The value of the other `LC_*` environment variables is simply ignored in this case. |
| `LC_COLLATE`  | <p>Specifies what collation order to use for string comparing and sorting. Example:</p><pre><code>LC_COLLATE=”en_US.UTF-8”</code></pre>                                                                                    |
| `LC_CTYPE`    | <p>Specifies what locale to use for character sets and character classification. Example:</p><pre><code>LC_CTYPE=”en_US.UTF-8”</code></pre>                                                                                |
| `LC_MESSAGES` | <p>Specifies what locale to use for printing messages and for parsing responses. Example:</p><pre><code>LC_MESSAGES=”en_US.UTF-8”</code></pre>                                                                             |
| `LC_MONETARY` | <p>Specifies what locale to use for formatting monetary values. Example:</p><pre><code>LC_MONETARY="en_US.UTF-8"</code></pre>                                                                                              |
| `LC_NUMERIC`  | <p>Specifies what locale to use for formatting numbers. Example:</p><pre><code>LC_NUMERIC="en_US.UTF-8"</code></pre>                                                                                                       |
| `LC_TIME`     | <p>Specifies what locale to use for formatting date/time values. Example:</p><pre><code>LC_TIME="en_US.UTF-8"</code></pre>                                                                                                 |
| `NLSPATH`     | <p>Specifies the directories in which the <code>catopen()</code> function looks for message translation catalogs. Example:</p><pre><code>NLSPATH="/system/nlslib/%N.cat"</code></pre>                                      |

## Character Encoding

Character enconding is the **process of maintaining the mapping between the character and its internal value.**

Common examples of character encoding systems are: Morse code, the American Standard Code for Information Interchange (ASCII), and Unicode.

To **determine the current character mapping in Linux**, execute the following <mark style="color:red;">`locale`</mark> command with <mark style="color:red;">`charmap`</mark>:

```bash
sysadmin@localhost:~$ locale charmap                                            
ANSI_X3.4-1968
```

To **display all available charmaps** (character maps) on the system, use the <mark style="color:red;">`locale`</mark> command with the <mark style="color:red;">`-m`</mark> option:

```bash
sysadmin@localhost:~$ locale -m                                            
ANSI_X3.110-1983                                                                
ANSI_X3.4-1968                                                                  
ARMSCII-8                                                                       
ASMO_449                                                                        
BIG5                                                                            
BIG5-HKSCS                                                                      
BRF                                                                             
BS_4730                                                                         
BS_VIEWDATA                                                                     
CP10007    
```

### ASCII

ASCII (American Standard Code for Information Interchange) is an encoding that is used to represent the English language letters, numbers, symbols and control codes as a 7-bit binary number. The standard ASCII character set includes 128 characters.

Out of the total 128 characters, 95 are printable characters, and the remaining 33 are non-printable control characters; these characters include:

* Characters
  * Numbers 0-9
  * Lowecase letters
  * Uppercase letters
* Punctuation symbols
* Control codes
* Blank space

ASCII's limited character set proved to be a big constraint in accommodating languages other than English. While it was the most commonly-used character encoding on the web until 2007, it was surpassed by UTF-8, which includes ASCII as a subset.

### Unicode

Unicode is a standard, designed to assign a unique number to every character of every language, regardless of the platform and programs being used.

Unicode has become the main scheme for internal processing and storage of text in modern computing. The Linux operating system can utilize the UTF-8 encoding scheme. Unicode characters can be included in filenames, and Unicode strings may be used as command-line parameters. Text editors can be used to display and edit files containing characters in Unicode format.

### UTF-8

UTF-8 (Unicode Transformation Format - 8 bit) is an encoding that can represent every character in the Unicode character set with 1 to 4 bytes. UTF-8 is backward-compatible with ASCII

UTF-8 encoding is widely-used for websites, as well as programming languages, operating systems, adn software applications.

In Linux, UTF-8 mode can be activated by setting the `LANG` enviroment variable to the appropiate locale.

Check the settings using the `locale` command. An output similar to below confirms the setting

```bash
sysadmin@localhost:~$ locale                                             
LANG=en_US.UTF-8                                                                           
LANGUAGE=                                                                      
LC_CTYPE="en_US.UTF-8"                                                                
LC_NUMERIC="en_US.UTF-8"                                                              
LC_TIME="en_US.UTF-8"                                                                 
LC_COLLATE="en_US.UTF-8"        
```

### ISO-8859

ISO/IEC 8859 is a joint International Organization for Standardization (ISO) and International Electrotechnical Commission (IEC) series of standards for 8-bit character encodings.

While the bit patterns of the 95 printable ASCII characters are sufficient to exchange information in modern English, most other languages need additional symbols not covered by ASCII. ISO/IEC 8859 solved this problem by utilizing the eighth bit in an 8-bit byte, which allows an additional 96 printable characters to be accommodated.

### Conversion from One Character Encoding to Another

The <mark style="color:red;">**`iconv`**</mark> command is the standard application programming interface (API) for **converting one character encoding to another.** The <mark style="color:red;">`iconv`</mark> command is suitable to convert characters in a large number of files.

The <mark style="color:red;">`iconv -l`</mark> command gives the **list of supported encodings.**

The generic syntax for converting encoding of given files from one encoding to another using <mark style="color:red;">`iconv`</mark> is as follows:

```bash
iconv –f old-encoding [-t new-encoding] file.txt > newfile.txt
```

For example, the following will convert the file `test1.txt` from the ISO 8859-1 standard code set to the CP 437 standard code set and store the output in the file named `test1convert.txt`:

```bash
sysadmin@localhost:~$ iconv -f  ISO8859-1 -t CP437 test1.txt > test1convert.txt
```

## Time Zone, Date, and Time

The time zone for a Linux system is set at the tie of installation and does not require changes very often. However, in a multi-user scenario, users may require different time zones.

Linux (and UNIX) computers keep time in Universal Time (UTC). Linux systems internally keep time using aa UTC-synchronized clock that is converted to the appropiate local time based upon user preferences.

The <mark style="color:red;">`TZ`</mark> enviroment **variable is used to determine the time zone** and how to calculate local time.

### Local Time Zone Configuration File

The <mark style="color:orange;">`/etc/localtime`</mark> file is the **local time zone configuration file** that **stores the system-wide time zone of the local system** used by applications for presentation to the user. This file contains binary data and is not edited directly.

It is recommended to backup the file before changing the time zone.

### Checking Current System Time Zone

On a **Debian**-derived system, the <mark style="color:orange;">`/etc/timezone`</mark> **shows the current time zone**:

```bash
sysadmin@localhost:~$ cat /etc/timezone
Etc/UTC
```

For **Red Hat**-derived systems, the <mark style="color:orange;">`/etc/sysconfig/clock`</mark> file is **used to indicate the current time zone**:

```
[sysadmin@localhost ~]$ cat /etc/sysconfig/clock
ZONE=”America/Los_Angeles"
```

### Time Zone Information Directory

The `/usr/share/zoneinfo` directory contains all the time zones supported. The following command will list all the zones:

```
sysadmin@localhost:~$ ls /usr/share/zoneinfo    
Africa      Cuba     GMT+0      Kwajalein  Poland     WET                       
America     EET      GMT-0      Libya      Portugal   Zulu                      
Antarctica  EST      GMT0       MET        ROC        iso3166.tab               
Arctic      EST5EDT  Greenwich  MST        ROK        leap-seconds.list         
Asia        Egypt    HST        MST7MDT    Singapore  localtime                 
Atlantic    Eire     Hongkong   Mexico     SystemV    posix                     
Australia   Etc      Iceland    NZ         Turkey     posixrules                
Brazil      Europe   Indian     NZ-CHAT    UCT        right                     
CET         Factory  Iran       Navajo     US         zone.tab                  
CST6CDT     GB       Israel     PRC        UTC        zone1970.tab
```

### Changing the Time Zone using the `tzselect` method

The <mark style="color:red;">`tzselect`</mark> utility **helps determine the time zone value used to set the **<mark style="color:red;">**`TZ`**</mark>** variable for a particular location** by allowing the user to choose the country and location from a set of menus.

Once the <mark style="color:red;">`tzselect`</mark> utility returns the desired time zone, the user must set the <mark style="color:red;">`TZ`</mark> variable by following the instructions provided by <mark style="color:red;">`tzselect`</mark>

For example, if a user is in the US state of California and uses the <mark style="color:red;">`tzselect`</mark> command, it would inform the user to set the `TZ` variable to the value "America/Los\_Angeles". When running the <mark style="color:red;">`tzselect`</mark> command, it will ask questions, starting with the continent or ocean related to a specific time zone:

```bash
sysadmin@localhost:~$ tzselect
Please identify a location so that time zone rules can be set correctly.   
Please select a continent, ocean, "coord", or "TZ".                             
 1) Africa                                                                      
 2) Americas                                                                    
 3) Antarctica                                                                  
 4) Arctic Ocean                                                                
 5) Asia                                                                        
 6) Atlantic Ocean                                                              
 7) Australia                                                                   
 8) Europe             
```

After providing the time zone region of `21` (Pacific Time) and answering the validation prompt, the following information is provided:

```bash
The following information has been given:
 
        United States                                                           
        Pacific Time                                                            
 
        Therefore TZ='America/Los_Angeles' will be used.                                
Local time is now:      Wed Sep 25 12:30:35 PDT 2019.                           
Universal Time is now:  Wed Sep 25 19:30:35 UTC 2019.                           
Is the above information OK?                                                    
1) Yes                                                                          
2) No
#? 1
```

To set the time zone temporarily and verify it worked, execute the following commands:

```bash
sysadmin@localhost:~$ date                                                      
Wed Sep 25 19:31:41 UTC 2019                                                     
sysadmin@localhost:~$$ TZ='America/Los_Angeles'; export TZ                        
sysadmin@localhost:~$ date                                                      
Wed Sep 25 12:32:40 PST 2019
```

To make the time zone change permanent for a user, append the following lines to the `~/.profile` or `~/.bash_profile` in the user's home directory.

```
TZ=’America/Los_Angeles’; export TZ
```

The change will be effective once the user logs out and logs in again. Use the <mark style="color:red;">`date`</mark> command to confirm the new time zone setting.

{% hint style="info" %}
Adding the `TZ` variable to one specific user profile changes the time zone for that user only and does not affect the time zone for other users or the default system time zone&#x20;
{% endhint %}

{% hint style="warning" %}
**Consider this**

Other Linux distributions may use different approaches to change the time zone. Popular commands are:

* `dpkg-reconfigure tzdata`
* `redhat-config-date`
* `system-config-date`
{% endhint %}

### Changing the System Time Zone Using the Command Line Method

This procedure requires an administrator to log in as the root user. Before changing the time zone, first, check the current time zone by using the <mark style="color:red;">`date`</mark> command.

```
sysadmin@localhost:~$ date                                                      
Wed Sep 25 20:18:08 UTC 2019
```

The previous example shows UTC (Coordinated Universal Time ) as the current time zone.

The `/usr/share/zoneinfo` directory contains a list of time zone regions. Display the contents of this directory:

```
sysadmin@localhost:~$ ls /usr/share/zoneinfo                                    
Africa      Cuba     GMT+0      Kwajalein  Poland     WET                       
America     EET      GMT-0      Libya      Portugal   Zulu                      
Antarctica  EST      GMT0       MET        ROC        iso3166.tab               
Arctic      EST5EDT  Greenwich  MST        ROK        leap-seconds.list         
Asia        Egypt    HST        MST7MDT    Singapore  localtime                 
Atlantic    Eire     Hongkong   Mexico     SystemV    posix                     
Australia   Etc      Iceland    NZ         Turkey     posixrules  
```

To set a new system time zone:

1. Find the city that represents the desired time zone by either:
   1. Using the <mark style="color:red;">`tzselect`</mark> utility, or
   2. Searching the time zone directory by first changing to the appropiate continent or ocean subdirectory

```bash
sysadmin@localhost:~/Documents$ ls /usr/share/zoneinfo/Europe/Madrid            
/usr/share/zoneinfo/Europe/Madrid
```

2\. Before changing the time zone, backup the current time zone settings file using the following command:

```bash
root@localhost:~ cp /etc/localtime  /etc/localtime.org
```

3\. Create a symbolic link for the machine's clock to the city in the new time zone

```bash
root@localhost:~ ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
```

4\. Verify the new time zone by running the `date` command again

```bash
sysadmin@localhost:~/Documents$ date                                            
Mon May 16 18:46:51 CEST 2022   
```

### Setting System Date and Time

#### Method 1

The root user can use the `date` command to set the system date and time

```
date MMDDhhmmYYYY.ss
```

For example, to set the time and date value to Mar 29, 15:26:07 2020, execute the following command:

```
root@localhost:~# date 032915262020.07
```

#### Method 2

Use the <mark style="color:red;">`-s`</mark> or <mark style="color:red;">`--set`</mark> option to set the system date and time using a more user-friendly notation:

```
root@localhost:~# date -s “Mon Mar 23 17:00:00 UTC 2020”
Mon Mar 23 17:00:00 UTC  2020
```

{% hint style="info" %}
The <mark style="color:red;">`date`</mark> command is very powerful and useful beyond simply displaying or setting the current date and time. In addition to the many output format options, <mark style="color:red;">`date`</mark> is also used to display a relative date (<mark style="color:red;">`date --date="next mon`</mark>`"`), past dates (<mark style="color:red;">`date --date="1 year ago"`</mark>), Universal time and the last modification time of a file. It is worthy of further exploration.
{% endhint %}

{% hint style="warning" %}
On <mark style="color:red;">`systemd`</mark> systems, the <mark style="color:red;">`timedatectl`</mark> command is used as a replacement for the <mark style="color:red;">`date`</mark> command to view and configure system time.
{% endhint %}
