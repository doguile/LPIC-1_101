# Module 3: Working with text

## Chapter 7: Text Utilities

<details>

<summary>Key Terms</summary>

`cat` Displays the contents of one or more files to standard output

`cut` Command used to extract fields of information from a text file

`head` Prints the first 10 lines of a file by default

`less` Pager command used to view file contents one page of data at a time

`nl` Prepend a number to the lines of a file incrementing each line by 1 by default

`od` Performs an octal dump of data by default. Often used to display the contents of files with non-printable characters.

`paste` Merge the lines of one or more files, line by line, separating the output by tabs by default

`sed` A non-interactive editar that can be used to modify text

`sort` COmmand used to display a file sorted on a specific field of data

`split` Command used to take one file and break it into multiple parts. By default it breaks the file up int 1000 line

`tail` Prints the last 10 lines of a file by default

`tr` Command used to translate one set of characters to another

`uniq` Command to remove duplicates lines that are currently consecutive

`wc` Print newline, word, and byte counts for each FILE, and a total line if more than one FILE is specified.

</details>

## Viewing File Content

With a single file argument, the <mark style="color:red;">`cat`</mark> command will simply output the contents of a file:

```
cat [OPTION]...[FILE]
```

This command has the **ability to accept two or more files as input** and then output the concatenation of those files.

To display the file one page at a time, use a pager, either the <mark style="color:red;">`more`</mark> or the <mark style="color:red;">`less`</mark> command.

To view a file with the `less` command, pass the file name as an argument:

```
less FILE
```

The <mark style="color:red;">`more`</mark> and <mark style="color:red;">`less`</mark> commands allow users to navigate the document using keystroke commands.

## Splitting files

The <mark style="color:red;">`split`</mark> command can take one file and split it into multiples files.&#x20;

{% hint style="info" %}
This can also be a useful way to send larger files across a slow network that has connection issues
{% endhint %}

Once the pieces are copied to the other system, **the **<mark style="color:red;">**`cat`**</mark>** command can be used to reassemble the pieces into a single file once again.**

```
split [OPTION]...[INPUT [PREFIX]]
```

By default, **the new files will be named with a prefix of x and an alphabetical suffix of aa, ab, etc**.

```bash
sysadmin@localhost:~/Documents$ split longfile.txt
sysadmin@localhost:~/Documents$ ls                                              
School            alpha.txt    linux.txt     profile.txt   xac  xai             
Work              animals.txt  longfile.txt  red.txt       xad  xaj             
adjectives.txt    food.txt     newhome.txt   spelling.txt  xae  xak             
alpha-first.txt   hello.sh     numbers.txt   words         xaf                  
alpha-second.txt  hidden.txt   os.csv        xaa           xag                  
alpha-third.txt   letters.txt  people.csv    xab           xah
```

As indicated by the command syntax, **it is possible to specify a prefix other than x**. For example the following examples denotes `file.` as the prefix

```
split longfile.txt file.
file.aa
file.ab
file.ac
```

**The **<mark style="color:red;">**`-d`**</mark>** option will allow for the split files to have numeric suffix** instead of a default alphabetic suffix.

```
split longfile.txt -d file.
file.00
file.01
file.02
```

By default, the <mark style="color:red;">`split`</mark> command will break apart a file into 1,000 line chunks.

**The **<mark style="color:red;">**`-l`**</mark>** option can be used to specify the number of lines to split upon**. Alternatively, the <mark style="color:red;">`-b`</mark> option may be used to specify the maximum number of bytes to use per file.

```
split -d longfile.txt file. -l 5000
```

## Numbering the Output of Files

The <mark style="color:red;">`nl`</mark> command will number the lines of its output.&#x20;

```
nl [OPTION]...[FILE]
```

By default, **it only number non-empty lines**.&#x20;

```bash
sysadmin@localhost:~/Documents$ nl newhome.txt
     1  Thanks for purchasing your new home!!
 
     2  **Warning** it may be haunted.
 
     3  There are three bathrooms.
 
     4  **Beware** of the ghost in the bedroom.
 
     5  The kitchen is open for entertaining.
 
     6  **Caution** the spirits don't like guests.
 
     7  Good luck!!!
```

To have the <mark style="color:red;">`nl`</mark> command **number every line** in the .txt file, execute the following command:

```bash
sysadmin@localhost:~/Documents$ nl -ba newhome.txt
     1  Thanks for purchasing your new home!!
     2
     3  **Warning** it may be haunted.
     4
     5  There are three bathrooms.
     6
     7  **Beware** of the ghost in the bedroom.
     8
     9  The kitchen is open for entertaining.
    10
    11  **Caution** the spirits don't like guests.
    12
    13  Good luck!!!
```

**The **<mark style="color:red;">**`-b`**</mark>** option is the **<mark style="color:red;">**`--body-numbering`**</mark>** option**, abbreviated in its short version as <mark style="color:red;">-b</mark>. The `a` is actually an argument that means number all lines. In other words, the command could also be executed:

```bash
  nl -b a newhome.txt
  nl --body-numbering=a newhome.txt
```

{% hint style="info" %}
It is also possible to number lines using the <mark style="color:red;">`cat`</mark> command with the <mark style="color:red;">`-n`</mark> option, which numbers every line by default
{% endhint %}

## Displaying the Beginning of a File

The purpose of the <mark style="color:red;">`head`</mark> command is to view the beginning of a file or output

```
head [OPTIONS]...[FILE]
```

By default, the <mark style="color:red;">`head`</mark> command will display the first ten lines of a file's contents.&#x20;

For instance, there is the ability to use a number as an option to indicate how many lines of output to display. For example, to display the first three lines of a file, execute:

```
head -3 file.txt
```

There is also the <mark style="color:red;">`-n`</mark> option which takes an argument for the number of lines to display. So, the following would also display the first three lines of the file

```
head -n3 file.txt
```

A negative number can also be used as an argument to the <mark style="color:red;">`-n`</mark> option, which tells the <mark style="color:red;">`head`</mark> command how many lines to _omit from the bottom of the file_. For example, when executing the <mark style="color:red;">`head -n -90`</mark> command on a file that is 100 lines long, the output would only include the first 10 lines by omitting the last 90.

```
head -n -24 file.txt
```

## Displaying the End of a File

The opposite of the <mark style="color:red;">`head`</mark> command, the <mark style="color:red;">`tail`</mark> command, displays **contents from the end of the file** and not the beginning.

```
tail [OPTIONS]...[FILE]
```

The <mark style="color:red;">`tail`</mark> command also displays ten lines by default with no options given:

```bash
~/Documents$ tail alpha.txt
Q is for Quark
R is for Rat
S is for Sloth
T is for Turnip
U is for Up
V is for Velvet
W is for Walrus
X is for Xenon
Y is for Yellow
Z is for Zebra
```

**You may use either a number of the **<mark style="color:red;">**`-n`**</mark>** option as an argument** to the tail command to specify how many lines you want to output from the end of the file.

```
tail -n3 alpha.txt
tail -3 alpha.txt
```

When using the <mark style="color:red;">`-n`</mark> option with a number prefixed by a plus sign + character, the number is interpreted as the line number in the file to start displaying content from; **it will display from that line to the end of the file.**&#x20;

```bash
sysadmin@localhost:~/Documents$ tail -n +20 alpha.txt
T is for Turnip
U is for Up
V is for Velvet
W is for Walrus
X is for Xenon
Y is for Yellow
Z is for Zebra
```

{% hint style="info" %}
When the <mark style="color:red;">`tail`</mark> command is executed with the <mark style="color:red;">`-f`</mark> option and a file name argument, then it will initially output the number of lines specified. Instead of exiting after displaying the lines, the <mark style="color:red;">`tail`</mark> command continues to run, following any changes in the file and displaying new content as it is added to the end of the file.
{% endhint %}

## **Combining File Output**

### **Command `paste`**

**The **<mark style="color:red;">**`paste`**</mark>** command will merge the lines of one or more files, line by line,** separating them with a _tab as a delimiter_ by default.

To demonstrate the `paste` command’s functionality, the `numbers.txt` and `letters.txt` will be used:

{% tabs %}
{% tab title="Files to paste" %}
```bash
sysadmin@localhost:~/Documents$ cat numbers.txt
1
2
3
4
5
sysadmin@localhost:~/Documents$ cat letters.txt
a
b
c
d
e
```
{% endtab %}

{% tab title="Command paste" %}
```bash
sysadmin@localhost:~/Documents$ paste numbers.txt letters.txt
1       a
2       b
3       c
4       d
5       e
```
{% endtab %}
{% endtabs %}

It is also possible to use other characters as delimiters. The <mark style="color:red;"></mark> <mark style="color:red;"></mark><mark style="color:red;">`-d`</mark> option is used to specify the delimiter.

```bash
sysadmin@localhost:~/Documents$ paste -d , numbers.txt letters.txt
1,a
2,b
3,c
4,d
5,e
```

### Command `join`

Similar to the <mark style="color:red;">`paste`</mark> command, the <mark style="color:red;">`join`</mark> command is able to combine two files. **The **<mark style="color:red;">**`join`**</mark>** command matches the values of fields to determine which lines to combine.**

In order to break a line into fields, the <mark style="color:red;">`join`</mark> command uses either tabs or spaces between the contents of the files and breaks each line into separate fields.

```
join [OPTION]... FILE1 FILE2
```

To demonstrate this properly, we will use the following two files.

{% tabs %}
{% tab title="Files to join" %}
```bash
sysadmin@localhost:~/Documents$ cat adjectives.txt
1 golden
2 honey
3 fruit
4 grey
5 bald
sysadmin@localhost:~/Documents$ cat animals.txt
1 retriever 
2 badger
3 bat
4 wolf
5 eagle
```
{% endtab %}

{% tab title="Command join" %}
```bash
sysadmin@localhost:~/Documents$ join adjectives.txt animals.txt
1 golden retriever
2 honey badger
3 fruit bat
4 grey wolf
5 bald eagle
```

**If the data that is used to join the files is the first field, then there is no need to specify which join fields to use**. However, the field that is used in the joining process must be sorted in both files first, or else errors may occur.
{% endtab %}
{% endtabs %}

Use the <mark style="color:red;">`-t`</mark> option to specify an alternative **delimiter**. For example if two spreadsheets were exported into comma separated value `.csv` files, join could use those commas to distinguish one field from another.

```bash
sysadmin@localhost:~/Documents$ cat people.csv
Dennis,Richie
Andrew,Tanenbaum
Ken,Thompson
Linus,Torvalds

sysadmin@localhost:~/Documents$ cat os.csv
1970,Unix,Richie
1987,Minix,Tanenbaum
1970,Unix,Thompson
1991,Linux,Torvalds
```

The following <mark style="color:red;">`join`</mark> command will join the files according to the field containing last names:

```bash
join -1 2 -2 3 -t',' people.csv os.csv
```

To specify that the join field for the first file is the second field, `-1 2` is used. The `-1` option means "field of the first file" and the `2` argument means "the second field".

To specify that the join field for the second file is the third field, `-2 3` is used. The `-2` option means "field of the second file" and the `3` argument means "the third field".

These files also use a delimiter that is neither a tab nor a space, so the delimiter is specified as a comma , character with the `-t','` option:

```bash
sysadmin@localhost:~/Documents$ join -1 2 -2 3 -t',' people.csv os.csv
Richie,Dennis,1970,Unix
Tanenbaum,Andrew,1987,Minix
Thompson,Ken,1970,Unix
Torvalds,Linus,1991,Linux
```

## Command Line Pipes

The pipe `|` character can be used to send the output of one command to another:

```
COMMAND 1 | COMMAND 2 | COMMAND 3
```

The `head` and `tail` commands are often used in command pipelines to limit the number of lines when dealing with large files. For example, to number only the last three lines of the `alpha.txt` file, pipe the output of the `tail` command to the `nl` command:

```bash
sysadmin@localhost:~/Documents$ tail -3 alpha.txt | nl
     1  hello                                                         
     2  inkling                                                       
     3  jogger
     
sysadmin@localhost:~/Documents$ ls | head -5
School                                                                          
Work                                                                            
adjectives.txt                                                                  
alpha-first.txt                                                                 
alpha-second.txt
```

## Extract Fields in a File

### `cut` Command

The <mark style="color:red;">`cut`</mark> command **extracts fields of information from a text fil**e.

```
cut [OPTION]...[FILE]
```

Note the output of the following `head` command for the first line of the `/etc/passwd` command. There are 7 fields, each separated by the colon `:` character as a delimiter:

```
sysadmin@localhost:~/Documents$ head -1 /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

To modified a separator you can use the delimiter <mark style="color:red;">`-d`</mark> option. Use the <mark style="color:red;">`-f`</mark> option to specify a list of comma separated field number to display or a hyphenated range of fields to display

```bash
sysadmin@localhost:~/Documents$ head -1 /etc/passwd | cut -d: -f1,5,6,7
root:root:/root:/bin/bash

sysadmin@localhost:~/Documents$ head -1 /etc/passwd | cut -d: -f1,5-7
root:root:/root:/bin/bash
```

Given a file that has fields at fixed character positions, the `cut` command can be used to extract the characters one at a time by using the character <mark style="color:red;">`-c`</mark> option followed by a range.

For example, in the `/var/syslog` file; the first fifteen characters specify a timestamp. To extract only those characters from the text, specify `1` through `15`:

```bash
sysadmin@localhost:~/Documents$ tail /var/log/syslog | cut -c1-15
Jul 20 15:17:01
Jul 20 15:59:02
Jul 20 16:17:01
Jul 20 16:41:22
Jul 20 16:59:02
```

## Sort File Output

### `sort` Command

The <mark style="color:red;">`sort`</mark> command is used to display a file sorted on a specific field of data

```
sort [OPTION]...[FILE]...
```

&#x20;Using <mark style="color:red;">`sort`</mark> with no option on a file results in the lines being sorted in ASCII order, which is similar to alphabetical order:

```bash
sysadmin@localhost:~/Documents$ sort people.csv
Andrew,Tanenbaum
Dennis,Richie
Ken,Thompson
Linus,Torvalds
```

To specify an alternate delimiter, use the <mark style="color:red;">`-t`</mark> option. To specify the fields to sort from first to last, use one or more <mark style="color:red;">`-k`</mark> options. In the following example, the `os.csv` file is sorted based on the second field of data, using the comma `,` character as a delimiter:

```
sysadmin@localhost:~/Documents$ sort -t',' -k2 os.csv
1991,Linux,Torvalds
1987,Minix,Tanenbaum
1970,Unix,Richie
1970,Unix,Thompson
```

In order to have the <mark style="color:red;">`sort`</mark> command treat a field numerically, add an <mark style="color:red;">`n`</mark> as an argument to the <mark style="color:red;">`-k`</mark> option for that key field specification. The <mark style="color:red;">`1`</mark> indicates the first field, and the <mark style="color:red;">`n`</mark> specifies sorting that field numerically:

```bash
sysadmin@localhost:~/Documents$ sort -t',' -k1n os.csv
1970,Unix,Richie
1970,Unix,Thompson
1987,Minix,Tanenbaum
1991,Linux,Torvalds
```

To reverse the sort direction from ascending to descending, add an <mark style="color:red;">`r`</mark> argument to the key field specification:

```bash
sysadmin@localhost:~/Documents$ sort -t',' -k1nr os.csv
1991,Linux,Torvalds
1987,Minix,Tanenbaum
1970,Unix,Richie
1970,Unix,Thompson
```

The `sort` command can be combined with other commands to accomplish more sophisticated tasks. For example, the output of the `cut -f7 -d: /etc/passwd` command contains many duplicate values:

```
sysadmin@localhost:~/Documents$ cut -f7 -d: /etc/passwd | head -n4
/bin/bash
/usr/sbin/nologin
/usr/sbin/nologin
/usr/sbin/nologin
```

By piping this output to the `sort` command and using the _unique_ <mark style="color:red;">`-u`</mark> option, **duplicate lines will be removed from the output**:

```
sysadmin@localhost:~/Documents$ cut -f7 -d: /etc/passwd | sort -u
/bin/bash
/bin/sync
/usr/sbin/nologin
```

## Remove Duplicate Lines in a File

### `uniq` Command

The <mark style="color:red;">`uniq`</mark> command does what the _unique_ `-u` option did with the `sort` command.

There may be times when the lines should not be sorted first. The <mark style="color:red;">`uniq`</mark> command can simply remove lines that are currently consecutive.

Second, the count <mark style="color:red;">`-c`</mark> option to the <mark style="color:red;">`uniq`</mark> command outputs the number of duplicate that were counted.

```bash
sysadmin@localhost:~/Documents$ cut -f7 -d: /etc/passwd | sort | uniq -c
    3 /bin/bash
    1 /bin/sync                                                    
    23 /usr/sbin/nologin
```

## Display File Contents in Various Formats

The `od` command can output data in several different formats. One use of this command is to display the contents of a file when it contains non-printable characters.

```
od [OPTION]...[FILE]
```

In order to demonstrate the `od` command, the `hidden.txt` file will be used:

```
sysadmin@localhost:~/Documents$ cat hidden.txt
NDG LPIC-1 Curriculum
Is The Best
```

It seems straight-forward enough, but there are non-printable control characters  causing `cat` to return to the beginning of the first line. This is where `od` comes in handy. Use the <mark style="color:red;">`-c`</mark> option to examine ASCII characters:

```
sysadmin@localhost:~/Documents$ od -c hidden.txt
0000000    T   h   i   s      \r   i   s      \r   a      \r   h   i   d
0000020    d   e   n      \r   m   e   s   s   a   g   e      \r   N   G
0000040    D       L   P   I   C   -   1       C   u   r   r   i   c   u
0000060    l   u   m      \n   I   s       T   h   e       B   e   s   t
0000100    .  \n  \n                                                    
0000103
```

{% hint style="info" %}
The <mark style="color:red;">`od`</mark> command can be used file recovery, or to open a file that crashes other programs
{% endhint %}

As the name implies, `od` can display raw file content in octal form. It also supports hexadecimal and decimal output. This can be very useful when analyzing a precompiled binary, such as a virus:

```bash
sysadmin@localhost:~/Documents$ od -x hidden.txt
0000000 6854 7369 0d20 7369 0d20 2061 680d 6469
0000020 6564 206e 6d0d 7365 6173 6567 0d20 474e
0000040 2044 504c 4349 312d 4320 7275 6972 7563
0000060 756c 206d 490a 2073 6854 2065 6542 7473
0000100 0a2e 000a
0000103
```

## Translate File Characters

### `tr` Command

The <mark style="color:red;">`tr`</mark> command can be used to translate from one set of characters to another.

The following example will translate all of the lowercase letters in `alpha-first.txt` to uppercase letters:

```bash
sysadmin@localhost:~/Documents$ cat alpha-first.txt | tr 'a-z' 'A-Z'
A IS FOR ANIMAL
B IS FOR BEAR
C IS FOR CAT
D IS FOR DOG
E IS FOR ELEPHANT
F IS FOR FLOWER
```

The characters that the <mark style="color:red;">`tr`</mark> command is translating do not have to be in a range; they can simply be listed for each set of characters.

```bash
sysadmin@localhost:~/Documents$ cat alpha-first.txt | tr 'aeiou' '@&1*^'
A 1s f*r An1m@l   
B 1s f*r B&@r
C 1s f*r C@t
D 1s f*r D*g
E 1s f*r El&ph@nt
F 1s f*r Fl*w&r
```

Aside from performing a translate function, the <mark style="color:red;">`tr`</mark> command can also "squeeze" repeated characters and delete characters. In performing these functions, only one set of characters needs to be specified. For example, **to eliminate duplicates, use the **<mark style="color:red;">**`-s`**</mark>** option to squeeze repeats of characters from the first set:**

```
echo 'aaaaaaaaappleeeeee' | tr -s 'ae'
apple
```

Using the <mark style="color:red;">`-d`</mark> option will **delete the characters** in the first set:

```bash
sysadmin@localhost:~/Documents$ cat alpha-first.txt | tr -d 'AEIOUaeiou'
 s fr nml  
B s fr Br
C s fr Ct
D s fr Dg
 s fr lphnt
F s fr Flwr
```

## Stream Editor Command

### `sed` Command

The stream editor <mark style="color:red;">`sed`</mark> command is a non-interactive editor that can be **used to modify text.**

```
sed [OPTION]...{SCRIPT} [FILE]....
```

To do a simple search and replace operation, use the following script, or expression. This will search for the pattern between the first two slashes, and if it finds that text, then it replaces it with what is specified between the last two slashes.

```
s/PATTERN/REPLACEMENT/
```

In the `alpha-first.txt` file, to replace the word `Animal` with the word `Apple`, execute the following command:

```bash
sysadmin@localhost:~/Documents$ sed 's/Animal/Apple/' alpha-first.txt
A is for Apple
B is for Bear
C is for Cat
D is for Dog
E is for Elephant
F is for Flower
```

Unlike most filter commands, the <mark style="color:red;">`sed`</mark> command can modify the original file by using the <mark style="color:red;">`-i`</mark> option.The <mark style="color:red;">`-i`</mark> option allows for an optional argument, which will be an extension added to the original file. **The result is a new file which is a copy of the original file**. For example, if you wanted to backup the original file as `alpha-first.txt.original` file, execute the following:

```
sed -i'.original' 's/Animal/Apple/' alpha-first.txt
```

### The Global Modifier

To **replace** all occurences of the pattern, **add the global modifier **_**`g`**_** after the final slash**.

```
s/PATTERN/REPLACEMENT/g
```

```bash
sysadmin@localhost:~/Documents$ sed 's/oo/00/' food.txt
F00d is good.

sysadmin@localhost:~/Documents$ sed 's/oo/00/g' food.txt
F00d is g00d.
```

#### Insert or Append

The <mark style="color:red;">`sed`</mark> command is also able to **either insert text before a pattern or after a pattern**. For this do not use the `s` character before the first slash; **use an insert change with the **<mark style="color:red;">**`i\`**</mark> expression or an **append change with the **<mark style="color:red;">**`a\`**</mark>** expression.**

```
/PATTERN/i\TEXT\
/PATTERN/a\TEXT\
```

**Inserting** text with the <mark style="color:red;">`i\`</mark> expression affects the line before the line containing the search term:

```bash
sysadmin@localhost:~/Documents$ sed '/is/i\Hello' food.txt
Hello
Food is good.
```

**Appending** text with the `a\` expression affects the line after the line containing the search term:

```
sysadmin@localhost:~/Documents$ sed '/is/a\Hello' food.txt
Food is good.
Hello
```

The <mark style="color:red;">`sed`</mark> command can also be used to **search for a line of text containing a pattern and then delete** the lines that match. Place the pattern to search for between two slashes followed by a `d` to carry out this expression.

```
/PATTERN/d
```

```bash
sysadmin@localhost:~/Documents$ cat animals.txt
1 retriever
2 badger
3 bat
4 wolf
5 eagle
sysadmin@localhost:~/Documents$ sed '/a/d' animals.txt
1 retriever
4 wolf
```

To change an entire line that matches a pattern to something else, use a slash, the pattern to match, another slash , `c\`, and then the new text.

```
/PATTERN\c/REPLACEMENT
```

For example, to change a line containing `3` to `three`, use the following command:

```bash
sysadmin@localhost:~/Documents$ cat numbers.txt
1
2
3
4
5
sysadmin@localhost:~/Documents$ sed '/3\c/three' numbers.txt
1
2
three
4
5
```

{% hint style="warning" %}
Keep in mind the entire line will be replaced, not just the matching pattern:
{% endhint %}

The <mark style="color:red;">`sed`</mark> command normally parses only one expression to filter text. To use multiple expressions, use an <mark style="color:red;">`-e`</mark> option with each expression to modify the file.

```
sysadmin@localhost:~/Documents$ sed -e '/3/c\three' -e '/a/d' animals.txt
1 retriever
three
4 wolf
sysadmin@localhost:~/Documents$ sed -e '/a/d' -e '/3/c\three' animals.txt
1 retriever
4 wolf
```

## Counting Lines of File

The <mark style="color:red;">`wc`</mark> command can be used to analyze a text file. By default, <mark style="color:red;">**`wc`**</mark>** counts the number of lines, words, and byte counts** in a passage and outputs this information in the following format:

```
lines words bytes filename
```

Recall the `alpha-first.txt` file:

```
sysadmin@localhost:~/Documents$ cat alpha-first.txt
A is for Apple 
B is for Bear
C is for Cat
D is for Dog
E is for Elephant
F is for Flower
```

Using the <mark style="color:red;">`wc`</mark> command on the same file reveals the following:

```bash
sysadmin@localhost:~/Documents$ wc alpha-first.txt
 6 24 90 alpha-first.txtsysadmin@localhost:~/Documents$ wc alpha-first.txt
 6 24 90 alpha-first.txt

sysadmin@localhost:~/Documents$ wc alpha-first.txt -l
6 alpha-first.txt
sysadmin@localhost:~/Documents$ wc alpha-first.txt -w
24 alpha-first.txt
sysadmin@localhost:~/Documents$ wc alpha-first.txt -m
90 alpha-first.txt
```

The <mark style="color:red;">wc</mark> command reports that there are 6 lines, containing 24 words , totaling 90 bytes in length.

In the example, the <mark style="color:red;">`-l`</mark> option was used to count the number of lines. In the second example, the <mark style="color:red;">`-w`</mark> option was used to count the number of words. Finally, **the **<mark style="color:red;">**`-m`**</mark>** option indicates the number of characters.**

One might imagine that the <mark style="color:red;">`-c`</mark> option would count the number of characters, but <mark style="color:red;">`-c`</mark> is used to count the number of bytes

One last option, <mark style="color:red;">`-L`</mark> returns the maximum line length in the file

```bash
sysadmin@localhost:~/Documents$ wc alpha-first.txt -L
17 alpha-first.txt
```
