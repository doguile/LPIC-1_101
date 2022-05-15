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

The term _locale_ referts to a set of parameters that define the user's language, country, and any special variant preferences. These parameters include the following:

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

Locale definition files are used to define the language, territory, and code set information applicable to the user. Locale definition files use the following naming convention:

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

The `locale` command without any arguments gives the summary of each locale category ( `LC_x` ). The output from the command will appear similar to this:

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

If information about a specific locale category is needed, the category `-c` option for the locale command can be used:

```
sysadmin@localhost:~$ locale -c LC_NAME
LC_NAME                                                                         
%p%t%g%t%m%t%f                                                                  
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
ANSI_X3.4-1968
```

Alternatively, the keyword `-k` option wil lshow you information about a specific keyword. Keywords generally start with `LC_` or `LANG`

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

Listing All the Available Locales



















