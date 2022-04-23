# Module 2: Basic File Management

## Chapter 4: File Globbing

## Introduction

File globbing might sound like an unusual term, but the concept of globbing is probably a familiar one. Like a joker in a deck of cards, which you might be able to use as any card (a wildcard), glob characters are special characters that can be used to match file or path names.

For the details of how globbing works, you can view the man page on glob in section 7 of the manual pages by executing:

```bash
sysadmin@localhost:~$ man 7 glob
sysadmin@localhost:~$ apropos glob
sysadmin@localhost:~$ man -k glob
```

> _A string is a wildcard pattern if it contains one of the characters **'?', '\*', or '\['.** Globbing is the operation that expands a wildcard pattern into the list of path names matching the pattern._

Globbing is especially useful for file management since it can be used to match the path names to files that you want to manage.

## Asterisk <mark style="color:red;">`*`</mark> Character

The asterisk `*` wildcard can be **used to match any string**, including the empty string.

{% hint style="info" %}
An empty string is a string that has nothing in it. It is commonly referred to as `""`
{% endhint %}

In order to limit the <mark style="color:red;">**`*`**</mark> wildcard, so that it matches fewer file names, it can be combined with other characters.

```bash
sysadmin@localhost:~$ echo D*
Desktop Documents Downloads
```

The **`*`** character **can appear anywhere in the pattern**; it **can even appear multiple times**. It is possible to find a file that not only starts with a `D`, but also contains the letter `n`, by using the `D*n*` pattern:

```bash
sysadmin@localhost:~$ echo D*n*
Documents Downloads
```

{% hint style="danger" %}
It is case sensitive
{% endhint %}

## Question Mark <mark style="color:red;">`?`</mark> Character

The _question mark <mark style="color:red;">**`?`**</mark>_ character in a string **will match exactly one character**.

```bash
sysadmin@localhost:~$ cd /usr/bin
sysadmin@localhost:/usr/bin$ echo /usr/bin/? 
/usr/bin/[ /usr/bin/w
```

By adding more question mark <mark style="color:red;">`?`</mark> characters, **it is possible to match additional characters**. In order to see which files had two or three characters as their file names, executes the following two <mark style="color:red;">`echo`</mark> commands:

```bash
sysadmin@localhost:/usr/bin$ echo ??
‌⁠​​⁠​ du ex hd id nl od pg pr sg tr ul vi wc xz
sysadmin@localhost:/usr/bin$ echo ???
a2p apt awk cal cmp col cut dig env eqn fmt gpg lcf ldd man pdb pic ptx rcp rev
rsh s2p scp see seq ssh sum tac tbl tee tic toe top tty ucf who xxd yes zip
```

**The incorporation of other characters** to patterns using the question mark <mark style="color:red;">`?`</mark> character, including other wildcard characters, **allow for more specific searches**. For example, the `w??` pattern will match files that started with the letter `w`, and are exactly three characters long:

```
sysadmin@localhost:/usr/bin$ echo w??
who
```

While the `w??*` pattern will match files that started with the letter w, but are **at least three characters** long:

```bash
sysadmin@localhost:/usr/bin$ echo w??*
w.procps wall watch wget whatis whereis which who whoami write
```

## Brackets <mark style="color:red;">`[]`</mark> characters

By using the _square brackets_ <mark style="color:red;">`[]`</mark> characters, a set of characters can be enclosed that will be used to **match exactly one character specified inside the brackets**.

For example, using a pattern like <mark style="color:red;">`[abcd]??`</mark> will match file names that start with an `a,b,c, or d` character and that are three character long. Alternatively, **letters that are consecutive can also be expressed as a range** like the <mark style="color:red;">`[a-d]??`</mark> pattern.

{% hint style="info" %}
When the hyphen <mark style="color:red;">**`-`**</mark> character is used with square brackets, it is referred to as a _**range**_
{% endhint %}

**When placed inside the square brackets, the wildcard characters lose their wildcard meaning and only match themselves**. So, a pattern like `[*]*` would match a file name that begins with an `*` character. A pattern of `[?]*` would match a file name that begins with a `?` character.

```bash
sysadmin@localhost:~/Documents$ echo [*]*
* ** ***
sysadmin@localhost:~/Documents$ echo [?]*
? ?? ???
```

Even using square brackets inside of the square brackets will match a square bracket, so `[[]]*` would match a file name that starts with the square brackets:

Just as with the other wildcard characters, the square brackets can appear multiple times in a pattern. For example, to match a file in the `/usr/bin` directory that contains at least two digits in the filename , use the following sentence:

```
$ echo *[0-9][0-9]*
```

The **square brackets also support negating the set of characters** (also called _complementation_). If the first character inside of the square brackets is either an exclamation `!` character or a caret `^` character, then that first character has the meaning of _**not the following characters.**_

```bash
#Would match a file name that does not begin with a letter a through v
echo [!a-v]*
```
