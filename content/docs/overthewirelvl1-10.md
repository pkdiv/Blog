---
title: "Overthewire Bandit (Level 0 - 10)"
date: 2023-03-12T15:22:31+05:30
---

OvertheWire Bandit is a beginner friendly wargames, introducting the players to basic linux commands and preparing them for other more complex wargames. 

Link to the wargame : [OverTheWire Bandit](https://overthewire.org/wargames/bandit/bandit1.html)

____

## Level 0

The credentials to login to Level0 are

*username: bandit0*

*password: bandit0*

Connect to the server using
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

____

## Level 0 ➡ Level 1

Level 0 is a easy level. The password for *bandit1* is stored in a file called **readme**. 
The command to read the content of a file is cat. It read the contents of the file and displays it on the terminal. 

```bash
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL
```

____

## Level 1 ➡ Level 2

The password for *bandit2* is stored in a file named -(hypen). Using just *cat -* , the content of the file is not displayed on the terminal. This because -(hypen) when used alone reads the input from *stdin* (put simply accepts input from commnad line). *./*  refers to current directory. When a filename is follwerd by *./* it refers to a file in the current directory, so *./-* can be used to read the contents of the file named -(hypen).

```bash
bandit1@bandit:~$ ls
-
bandit1@bandit:~$ cat ./-
rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
```

____

## Level 2 ➡ Level 3

In this level the password for *bandit3* is stored in a file which has spaces in its name - **spaces in this filename**. In CLI space acts as a end of the filename. So to refer to a file with spaces in it's name, the space have to be prepended using \\(Backslash) or the name of the file is to be inserted in between "" (Quotation Marks).

```bash
bandit2@bandit:~$ ls
spaces in this filename
bandit2@bandit:~$ cat ./spaces\ in\ this\ filename
aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG
bandit2@bandit:~$ cat "spaces in this filename"
aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG
```

____

## Level 3 ➡ Level 4

Files can be hidden by prepennding a . (period) to the filename. These files are not listed when *ls* is executed. To view them , ls has a flag *-a* or *--all* , which lists all the files including the hidden ones.

```bash
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere/
bandit3@bandit:~/inhere$ ls -a
.  ..  .hidden
bandit3@bandit:~/inhere$ cat .hidden
2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe
```

____

# Level 4 ➡ Level 5 

The level goal states that the password is stored in the only human readable file in the **inhere** directory. The directory contains 9 files. The password can be found by using cat command on the files, but is a tedious process. The *file* command can be used to display the type of a file. By using *./\** , we can list the type of all the files in the directory. The list shows a file with type as *ASCII test*. ASCII is a standard encoding format used for text data.

```bash
bandit4@bandit:~$ ls
inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: Non-ISO extended-ASCII text, with NEL line terminators
./-file06: Non-ISO extended-ASCII text, with no line terminators, with escape sequences
./-file07: ASCII text
./-file08: data
./-file09: data
bandit4@bandit:~/inhere$ cat ./-file07
lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR
```
____

## Level 5 ➡ Level 6

The password for *bandit6* is stored in a file in one of the directories in the **inhere** directory. Opening each of the directories and reading the content of the files is a very unexiting task. *find* command can be used to search the directories using CLI with the given characteristics. Based on the characters of the file given, the *-size* flag can be used to filter based on size. The **c** in *1033c* represents bytes (Check the man pages for other size characters). Another flag *!-executable* can be used to filter based on file being executable. But the current level can be completed even without using this flag, as executable files are usually highlighted in terminals.  

```bash
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere/
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere02  maybehere04  maybehere06  maybehere08  maybehere10  maybehere12  maybehere14  maybehere16  maybehere18
maybehere01  maybehere03  maybehere05  maybehere07  maybehere09  maybehere11  maybehere13  maybehere15  maybehere17  maybehere19
bandit5@bandit:~/inhere$ find ./ -size 1033c
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU
```

____

## Level 6 ➡ Level 7

One of the files on the system contains the password for the next level. Its is impractical to search the whole file system for the file. So the *find* command can be used to search for the file. In this level the file has the charcteritic of being owned by the user *bandit7* - this can be specified by using the *-user* flag, owned by the group *bandit6* - this can be specified using *-group* flag and the size using *-size* flag.

```bash
bandit6@bandit:~$ find / -size 33c -user bandit7 -group bandit6 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S
```

____

## Level 7 ➡ Level 8

*find* command searches directories for files with the specified characteristics but cannot find data with in a file. The *grep* command is used to search for content within a file. For this level, the goal says that the password is stored next to the word **millionth** , so searching for the word **millionth** will find the password.

```bash
bandit7@bandit:~$ grep millionth data.txt
millionth       TESKZC0XvTetK0S9xNwm25STk5iWrBvP
```

____

## Level 8 ➡ Level 9

*uniq* command is used to omit repeated lines . But these lines have to be consecutive line. So the *sort* command can be used to sort the file. *-u* flag of the *uniq* command prints the only line that occurs once. So the content of the file are sorted and piped using | (pipe) to the *uniq -u* command.

| (pipe) connects the *stdout* of one command to *stdin* of the other command (put simply the output of one command acts becomes the input for the other)

```bash
bandit8@bandit:~$ sort data.txt | uniq -u
EN632PlfYiZbn3PhVK3XOGSlNInNE00t
```

____

## Level 9 ➡ Level 10

*strings* command prints the readable characters in a file. And similar to Level7 the password can be found by searching for the preceding characters, which are a few **=** characters. So the *strings* command and piping the output to *grep* the password can be found.

```bash
bandit9@bandit:~$ strings data.txt | grep =====
f========== theM
========== password
========== is
========== G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s
```