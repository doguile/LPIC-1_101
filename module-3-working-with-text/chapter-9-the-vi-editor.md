---
description: '103.8: Basic file editing v2  Weight: 3'
---

# Chapter 9: The vi Editor

## Intro

There are several advantages to the <mark style="color:red;">**`vi`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">****</mark> editor:

* The `vi` editor is available on every linux distribution. This is not true of any other editor.
* The `vi` editor can be executed both in CLI and GUI
* The core functions have been around for decades.

> The original `vi` editor was written by Bill Joy.

Most linux system don't include the original <mark style="color:red;">`vi`</mark>, but instead include an improved version of it, know as `vim`, for _vi improved._ On some distributions, the <mark style="color:red;">`vi`</mark>file will link to <mark style="color:red;">`vim`</mark>`.`

```bash
sysadmin@localhost:~$ which vi                                                  
/usr/bin/vi
sysadmin@localhost:/etc/alternatives$ ls -l vi                                  
lrwxrwxrwx 1 root root 17 Mar 22  2019 vi -> /usr/bin/vim.tiny
```

While in other distributions, an alias exists that will execute `vim` when the `vi` command is run.

```bash
[sysadmin@localhost ~]$ which vi
alias vi='vim'
/usr/bin/vimas
```

On the other end of the spectrum is the <mark style="color:red;">`emacs`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> editor, an "extensible, customizable, self-documenting real-time display editor". For programmers and others that need sophisticated editing tools in a command line environment, this piece of GNU software provides maximum capability and flexibility.

## Command Mode Movement

There are three modes used in the `vi` editor: _command mode_, _insert mode_, and _ex mode._

Initially the programs starts in _command mode_. **Command mode is used to type commands, such as those used to move around a document, manipulate text, and access the other two modes**. To return to command mode any time, press the <mark style="color:red;">**Esc**</mark> key.

> The `vi` works in a terminal environment where a mouse is useless

Movement commands in <mark style="color:red;">`vi`</mark> <mark style="color:red;"></mark><mark style="color:red;"></mark> have two aspects: _a motion and a optional number prefix_, which indicates how many times to repeat that motion. The general format is as follows:

```
[count] motion
```

#### Motion keys available

| Motion | Result                |
| ------ | --------------------- |
| `w`    | One word forward      |
| `b`    | One word back         |
| `^`    | Beginning of the line |
| `$`    | End of the line       |

{% hint style="info" %}
Since the upgrade to <mark style="color:red;">`vim`</mark>,it is also possible to use the arrow ←↓↑→ keys instead of `h j k l` respectively
{% endhint %}

These motions can be prefixed with a number to indicate how many times to perform the movement. For example, the `5h` command would move the cursor five characters to the left and `3w`would move the cursor three words to the right.

To move the cursor for a specific line number, type the line number followed by the `G`character. For example, to get to the fifth line of the file, type the `5G` command. The `1G` or `gg` commands can be used to go to the first line of the file,  while a single `G` will take you to the last line.

{% hint style="info" %}
To find out which line the cursor is currently on, use **Ctrl+G**
{% endhint %}

## **Command Mode Actions**

The `vi` editor do not use copy, cut and paste. Instead, it uses the following three commands:

| Standard | Vi         | Meaning |
| -------- | ---------- | ------- |
| cut      | `d`        | delete  |
| copy     | `y`        | yank    |
| paste    | `P` \| `p` | put     |

Either of the following general formats for action commands is acceptable

```
action [count] motion
[count] action motion
```

### Delete

Delete **removes the indicated text from the page and saves it into the buffer** (the buffer being equivalent to the clipboard). The following tables provides some common usage examples:

| Action | Result                              |
| ------ | ----------------------------------- |
| `dd`   | Deletes the current line            |
| `3dd`  | Deletes the next three lines        |
| `dw`   | Deletes the current word            |
| `d3w`  | Deletes the next three words        |
| `d4h`  | Deletes four characters to the left |

### Change

Change is very similar to delete. However, when using change, **the program is switched to insert mode** to allow inmediate changes to the text.

| Action | Result                             |
| ------ | ---------------------------------- |
| `cc`   | Change the current line            |
| `cw`   | Change the current word            |
| `c3w`  | change the next 3 words            |
| `c5h`  | change five characters to the left |

### Yank

Yank **places content into the buffer without deleting it**.

|       |                             |
| ----- | --------------------------- |
| `yy`  | yank the current line       |
| `3yy` | yank the next three lines   |
| `yw`  | yank the current word       |
| `y$`  | yank to the end of the line |

### Put

**Put places the text saved in the buffer either before or after the cursor position.** Notice that these are the only two options as put does not use the motions like the previous action commands

| Action | Result                       |
| ------ | ---------------------------- |
| `p`    | Put (paste) after the cursor |
| `P`    | Put before cursor            |

### Searching in `vi`

The `vi`program uses _search_. Search is more powerful than find because it **supports both literal text patterns and regular expressions.**

To **search forward** from the current position of the cursor, **use the `/` character to start the search**, type a search term, and then press the **Enter** key to begin the search. The cursor will move to the first match that is found.

To proceed to the next match using the same pattern,  press the **n** key. To go back to a previous match, press the **N** key.&#x20;

**To start searching backwards from the cursor position, start by typing `?`** , then type the pattern to search for matches and press the **Enter** key.

| Action  | Result                     |
| ------- | -------------------------- |
| `/term` | Search forwards for `term` |
| `?term` | Search backward for `term` |

## Insert mode

Insert mode is **used to add text to the document**. There are a few ways to enter insert mode from command mode, each differing by where the text insertion will begin.

|     |                                                     |
| --- | --------------------------------------------------- |
| `a` | Enter insert mode right after the cursor            |
| `A` | Enter insert mode at the end of the line            |
| `i` | Enter insert mode right before the cursor           |
| `I` | Enter insert mode at the beginning of the line      |
| `o` | Enter insert mode on a blank line after the cursor  |
| `O` | Enter insert mode on a blank line before the cursor |

## Ex Mode

Originally, the <mark style="color:red;">`vi`</mark> editor was called the <mark style="color:red;">`ex`</mark> editor. The name <mark style="color:red;">`vi`</mark> was the abbreviation of the visual command inside the <mark style="color:red;">`ex`</mark> editor that switched the editor to "visual" mode.

Eventually, the actual program file was renamed <mark style="color:red;">`vi`</mark>, and the <mark style="color:red;">`ex`</mark> editor became a link that pointed to the `vi` editor.

When the `ex` mode of the `vi` editor is being used, it is possible to view or change settings, as well as carry out file-related commands like opening, saving, or aborting changes to a file. **In order to get to the `ex` mode, type a colon `:` character in command mode.**&#x20;

|                 |                                                     |
| --------------- | --------------------------------------------------- |
| `:w`            | Write  the current file to the filesystem           |
| `:w filename`   | save a copy of the current file as filename         |
| `:w!`           | Force writting to the current file                  |
| `:1`            | Go to the line number 1 or whatever number is given |
| `:e` _filename_ | Open filename                                       |
| `:q`            | quit if no changes made to the file                 |
| `:q!`           | quit without saving changes to file                 |

Although the ex mode offers several ways to save and quit, **there's also the **<mark style="color:red;">**`ZZ`**</mark>** command that is available in command mode; this is the equivalent of **<mark style="color:red;">**`:wq`**</mark>. There are many more overlapping functions between ex mode and command mode.

&#x20;For example, ex mode can be used to navigate to any line in the document by typing the colon `:` character followed by the line number, while the `G` command can be used in command mode as previously demonstrated.
