---
title: "Overthewire Bandit (Level 11 - 20)"
date: 2023-03-26T22:06:02+05:30
---


## Level 10 ➡ Level 11

The password is stored in a file named *data.txt* and is base64 encoded. Linux has a inbuilt command *base64* to encode and decode base64. The *base64* command with the *-d* flag , followed by the name of the file, gives us the password for the next level.

```bash
bandit10@bandit:~$ ls
data.txt
bandit10@bandit:~$ cat data.txt
VGhlIHBhc3N3b3JkIGlzIDZ6UGV6aUxkUjJSS05kTllGTmI2blZDS3pwaGxYSEJNCg==
bandit10@bandit:~$ base64 -d data.txt
The password is 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
```

____
## Level 11 ➡ Level 12

Password of the next level is stored in the *data.txt* file with each character rotated by 13 positions. Which means that of the original data had the character 'a' it would be replaced by the character 'n' in the data file. So to retrieve the password, we should rotate the positions of each character by 13 characters backwards. 
(Note: Here rotating backward or forward doesn't effect the solution as we have 26 Alphanets in English and so 13 being its half gives the same solution irrespective of the direction)

```bash
bandit11@bandit:~$ ls
data.txt
bandit11@bandit:~$ cat data.txt
Gur cnffjbeq vf WIAOOSFzMjXXBC0KoSKBbJ8puQm5lIEi
bandit11@bandit:~$ cat data.txt |  tr '[a-zA-Z]' '[n-za-mN-ZA-M]'
The password is JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```


____
## Level 12 ➡ Level 13

Password for *bandit13* needs a bit of work . The password is a hexdump of a file that has been compressed repeteadly.
So we have to first get the original file from the hexdump and then repeatedly decompress the file until we get the password. 

*xxd* is a command to create and reverse hexdumps of files. The *-r* flag id used to specify that the input file is a hexdump and to reverse it. The text file following the command and the flag is the input file, which is followed by the name of the output file.

*file* command is used to find out the type of file. The *compressr1* is a **gzip** file. gzip is one of the tools used to compress files. The *gzip* command along with the *-d* flag is used to decompress the *.gz* file.

```bash
bandit12@bandit:~$ cd /tmp/new1
bandit12@bandit:/tmp/new1$ xxd -r data.txt compressr1
bandit12@bandit:/tmp/new1$ file compressr1
compressr1: gzip compressed data
bandit12@bandit:/tmp/new1$ mv compressr1 compressr1.gz
bandit12@bandit:/tmp/new1$ gzip -d compressr1.gz
bandit12@bandit:/tmp/new1$ ls
compressr1  data.txt
```

Again running the *file* command we see that the output of the previous gzip is another compress file but has been compressed using *bzip2* .
*bzip2* is similar to *gzip* and hence *-d* flag decompresses it.
 
```bash
bandit12@bandit:/tmp/new1$ file compressr1
compressr1: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/new1$ mv compressr1 compressr1.bz2
bandit12@bandit:/tmp/new1$ bzip2 -d compressr1.bz2
bandit12@bandit:/tmp/new1$ ls
compressr1  data.txt
```

We again get a gzip file and use the same commands to decompress it.

```bash
bandit12@bandit:/tmp/new1$ file compressr1
compressr1: gzip compressed data
bandit12@bandit:/tmp/new1$ mv compressr1 compressr1.gz
bandit12@bandit:/tmp/new1$ gzip -d compressr1.gz
```
The output of the previous iteration is a *.tar* file which is another compressing tool.

The format of the output files for futher iterations are the same as one of the previous iterations. The process is just to be repeted until we get the password for the next level.
```bash
bandit12@bandit:/tmp/new1$ file compressr1
compressr1: POSIX tar archive (GNU)
bandit12@bandit:/tmp/new1$ mv compressr1 compressr1.tar
bandit12@bandit:/tmp/new1$ tar -xvf compressr1.tar
data5.bin
```
```bash
bandit12@bandit:/tmp/new1$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/new1$ mv data5.bin data5.bin.tar
bandit12@bandit:/tmp/new1$ tar -xvf data5.bin.tar
data6.bin
```

```bash
bandit12@bandit:/tmp/new1$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/new1$ mv data6.bin data6.bin.bz2
bandit12@bandit:/tmp/new1$ bzip2 -d data6.bin.bz2
```

```bash
bandit12@bandit:/tmp/new1$ file data6.bin
data6.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/new1$ tar -xvf data6.bin
data8.bin
```

```bash
bandit12@bandit:/tmp/new1$ file data8.bin
data8.bin: gzip compressed data
bandit12@bandit:/tmp/new1$ mv data8.bin data8.bin.gz
bandit12@bandit:/tmp/new1$ gzip -d data8.bin.gz
```

After decompressing the file repeatedly we get the ASCII text as the final output.

```bash
bandit12@bandit:/tmp/new1$ file data8.bin
data8.bin: ASCII text
bandit12@bandit:/tmp/new1$ cat data8.bin
The password is wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```
____
## Level 13 ➡ Level 14

The password for the next level is not given rather a *sshkey.private* is given.Logins through ssh can be done in two ways - entering the password everytime a user logs in or configure ssh to use private and public keys to login.

For this level, *-i* flag of the ssh command can be used to specify the file containing the private key of the user *bandit14*.

The level goal also says that the password for the current level is stored in the file */etc/bandit_pass/bandit14*. (The password maybe not be required for the current level but is required to move to level 15)

```bash
bandit13@bandit:~$ ls
sshkey.private
bandit13@bandit:~$ ssh bandit14@localhost -i sshkey.private -p 2220
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
.
.
bandit14@bandit:~$
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
```
____
## Level 14 ➡ Level 15

The password for the next is to be obtained by sending the password of user *bandit14* to port 30000 on localhost. Localhost refers to the device itself - used to refer to services running on the local device.

*nc* - netcat is a tool that can be used to make TCP/UDP connection. To send data to a device on the network through a particular port, the command nc is followed by the hostname which is followed by the port number .

```bash
bandit14@bandit:~$ nc localhost 30000
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
Correct!
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
```
____
## Level 15 ➡ Level 16

The current level is similar to the previous level i.e, data has to be send to a service running on the local device but needs to be encrypted using SSL.

*openssl* is a tool used to deal with various properties of the SSL protcol. For the current level, openssl is used to open a secure connection to port 30001 on localhost.

The command *openssl s_client* is used to esablish a connection with a remote host. *-connect* is used to specify the hostname and the port seperated by a colon.

```bash
bandit15@bandit:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)
.
.
read R BLOCK
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
Correct!
JQttfApK4SeyHwDlI9SXGR50qclOAil1
```
____
## Level 16 ➡ Level 17

To complete this level we have a task simialr to the previous levels but the port on which the data is to be sent has to be determined. The level goal says that the it is one of the port between 31000 to 32000. To find out the port, the network has to be scanned on these port and to figure out the services running on them. 

*nmap* - network mapper is a tool used to perform various network scans. *-sV* flag can be used to find the services running on the scanned ports and lists them.

```bash
bandit16@bandit:~$ nmap localhost -p 31000-32000 -sV
Starting Nmap 7.80 ( https://nmap.org ) at 2023-04-03 11:14 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00013s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
.
.
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 98.44 seconds
```
The result of the *nmap* shows two ports on which openssl is running. One of these ports sends back the data while the other sends the password for the next level. Connecting to both the ports - port 31790 gives use the password of the next level.

```bash
bandit16@bandit:~$ openssl s_client localhost:31518
CONNECTED(00000003)
.
.
read R BLOCK
JQttfApK4SeyHwDlI9SXGR50qclOAil1
JQttfApK4SeyHwDlI9SXGR50qclOAil1

bandit16@bandit:~$ openssl s_client localhost:31790
CONNECTED(00000003)
.
.
read R BLOCK
JQttfApK4SeyHwDlI9SXGR50qclOAil1
Correct!
-----BEGIN RSA PRIVATE KEY-----
.
.
-----END RSA PRIVATE KEY-----

closed
```
____
## Level 17 ➡ Level 18

Two files are present in the home directory - **passwords.old** and **passwords.new** . The line that is changed between the two files is the password of the next level.

*diff* is a command used to find the differences between two files. *diff* follwed by the file names gives the output.

```bash
> ssh bandit17@bandit.labs.overthewire.org -p 2220 -i .\bandit17.txt

bandit17@bandit:~$ ls
passwords.new  passwords.old
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< f9wS9ZUDvZoo3PooHgYuuWdawDFvGld2   <---- specifies the line in the first file
---
> hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg    <---- specifies the line in the second file
```
____
## Level 18 ➡ Level 19

Trying to connect to the next level, the connection gets terminated.

```bash
> ssh bandit18@bandit.labs.overthewire.org -p 2220
.
.
Byebye !
Connection to bandit.labs.overthewire.org closed.
```

The password for user *bandit19* is stored in a file - **readme**, but logging in and reading the file is not possible, since the goal says that the **.bashrc** file is modified.

**.bashrc** - is a script file that gets executed on every user login. So chaning it changes the login behaviour.

Since login is not possible, the *ssh* command is to be used. Commands can be executed on login by specifying them in double quotes at the end of the *ssh* command. So using the *cat*  command , the file can be read without a session being established.

```bash
> ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
.
.
bandit18@bandit.labs.overthewire.org's password:
awhqfNnAbc1naukrpqDYcF95h7HoMTrC
```

____
## Level 19 ➡ Level 20

SUID is a bit set in the permission of a file. It allows to execute a command as an other user.In the file permissions the character 'x' is replaced by 's' to specify that the SUID bit has been set . The program *bandit20-do* is used to execute any command as the user *bandit20*.  

So with the *bandit20-do* program the password stored in the file **/etc/bandit_pass/bandit20**  can be read, as the owner of the file is the user *bandit20*. 

```bash
bandit19@bandit:~$ ls
bandit20-do
bandit19@bandit:~$ ls -l
total 16
-rwsr-x--- 1 bandit20 bandit19 14876 Feb 21 22:03 bandit20-do
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
VxCazJaVykI6W36BkBU0mJTCM8rR95XT
```
____