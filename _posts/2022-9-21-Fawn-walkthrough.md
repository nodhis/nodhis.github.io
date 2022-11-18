---
layout: post
title: "Fawn Walkthrough HTB"
---

The File Transfer Protocol (FTP) is a standard communication protocol used for the transfer of computer files from a server to a client on a computer network.FTP users may authenticate themselves with a clear-text sign-in protocol, normally in the form of a username and password, but can connect anonymously if the server is configured to allow it. For secure transmission that protects the username and password, and encrypts the content, FTP is often secured with SSL/TLS (FTPS) or replaced with SSH File Transfer Protocol (SFTP).


The original specification for the File Transfer Protocol was written by Abhay Bhushan and published as RFC 114 on 16 April 1971.

---

💻️ The Target Machine is : 10.129.181.55

---

A tip I would suggest, if you don't want to constantly retype the IP address you can opt to export the address to a variable whose name can be of your chosing. 
In turn you can call the variable as `$[variableName]` the **$** is important.
Conside the command below:
```
-$ export IP=10.129.181.55 
```
The command is entered directly into the terminal that would be currently running and will be only active in that terminal.
To test the command:
```
-$ echo $IP
10.129.181.55
```
### Answering HTB Questions 

> What does the 3-letter acronym FTP stand for? 
>> file transfer protocol  

> Which port does the FTP service listen on usually?   
>> 21

> What acronym is used for the secure version of FTP? 
>> SFTP

> What is the command we can use to send an ICMP echo request to test our connection to the target? 
>> ping

> From your scans, what version is FTP running on the target?
>> vsftpd 3.0.3

> From your scans, what OS type is running on the target?  
>> Unix

> What is the command we need to run in order to display the 'ftp' client help menu?
>> ftp -h

> What is username that is used over FTP when you want to log in without having an account?
>> anonymous

Anonymous and ftp can either be used in the case above

> What is the response code we get for the FTP message 'Login successful'?
>> 230

This can be viewed when connecting to FTP. This also comes into play when analysing packets of data when using wireshark. In addition such response codes are also common in http/https services.

> There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system.
>> ls

> What is the command used to download the file we found on the FTP server?  
>> get

To download files in bulk you can use mget instead.

## -------------------------| ENUMARATION PHASE |------------------------------

```
-$ sudo nmap -p- --min-rate 5000 -sV -sC $IP -v -oN scan.nmap 

---snip---

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.15.232
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
Service Info: OS: Unix


---snip---

```

**`-p-`** ==> scan all the 65535 ports.
**`-sV`** ==> Enumerate the version of the service that is open.
**`-sC`** ==> Use default scrips to enumerate certain things like the version, OS
**`-v`** ==> Show me everything that the tool is doing. Being verbose.
**`-oN`** ==> On a normal file output everything there hence the filename [scan.nmap]()

From the enumaration we learn so many things like the OS type, and the file stored.
Furthermore, anonymous login is allowed.
We get to see the version of the service that is running, which is useful if that particular version has vulnerabilities that have been exploited and documented in databases (DB) like [exploitDB](https://www.exploit-db.com/).

## ----------------------------| EXPLOITAION |---------------------------------

With what we have discovered we can try and gain foothold on the machine 🤔️.

```
─$ ftp $IP             

Connected to 10.129.181.55.
220 (vsFTPd 3.0.3)
Name (10.129.181.55:olapapi): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls
229 Entering Extended Passive Mode (|||31975|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
ftp> get flag.txt 
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||39738|)
150 Opening BINARY mode data connection for flag.txt (32 bytes).
100% |***************************|    32        2.54 KiB/s    00:00 ETA
226 Transfer complete.
32 bytes received in 00:00 (0.16 KiB/s)
ftp> exit
221 Goodbye.

```

From the above you can see the response codes and their meaning which is quite helpful.
Since the flag is downloaded on our end we can easily view the contents.

> Submit root flag
>> 035db21c881520061c53e0536e44f815
