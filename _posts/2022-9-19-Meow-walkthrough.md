---
title: "Meow Walkthrough HTB"
layout: post
---
## Connecting to Hack the Box.

The first challenge you might face is as a beginner is basically connecting to the HTB. 
I would assume that you have already download .opvn file which will be in your /Downloads/.
Open your terminal and cd aka change directory to your downloads directory 
`cd ~/Downloads` 


Please note:
```
~ This is normally refered to as the tilde and represents your /home/[user] directory together.
/ This represents the root directory, when it is used alone or at the beginning.
. This represents your current directory.
.. This represents your previous directory.
```
Be keen on the above since you definitely encounter them in commands.

Once you are in your downloads directory, enter the following command.

```
$ sudo openvpn starting-point-[username].opvn

---snip---

sequence complete

```
So `sudo` is a command that allows a permitted user to execute a command as the **superuser** or **another user** as specified by the security policy.
In our case we are seeking **superuser** priveleges in order to use the command `openvpn` the [Starting-point-[username\].opvn]() is the file we are trying to use to access the HTB networks, This is what basically a vpn does, it connects us directly to a given network with security in mind. 

To confirm if you are truly connected use the command `ifconfig`

You should see this:

	tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 10.10.15.232  netmask 255.255.254.0  destination 10.10.15.232
        inet6 fe80::fb91:756c:dbd9:5da9  prefixlen 64  scopeid 0x20<link>
        inet6 dead:beef:2::11e6  prefixlen 64  scopeid 0x0<global>
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        RX packets 201639  bytes 8065580 (7.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 362980  bytes 21778633 (20.7 MiB)
        TX errors 0  dropped 321123 overruns 0  carrier 0  collisions 0

### Generating the machine.

On the HTB website you can press the [spawn machine](). Give it a few seconds and you should see an ip address.
To test if the ip address is active you can `ping` it. Consider the command below, press `ctrl+c` to end the ping.
```
$ ping 10.129.141.194

---snip---

64 bytes from 10.129.141.194: icmp_seq=3 ttl=63 time=184 ms

---snip---
```
This means that the machine is active and responsive.

---

💻️ The Target Machine is : 10.129.141.194

---

A tip I would suggest, if you don't want to constantly retype the IP address you can opt to export the address to a variable whose name can be of your chosing. 
In turn you can call the variable as `$[variableName]` the **$** is important.
Conside the command below:
```
-$ export IP=10.129.141.194 
```
The command is entered directly into the terminal that would be currently running and will be only active in that terminal.
To test the command:
```
-$ echo $IP
10.129.141.194
```
Another tip I would suggest if you are using a terminal like Tmux you can open multiple tabs like browser tabs and you can switch from one tab to the other on the same terminal.
Just look at the [file]() section on your terminal it should be on the top-left.

---

### Answering HTB Questions 

> What does the acronym VM stand for? 
>> Virtual Machine

> What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell.
>> Terminal

>  What service do we use to form our VPN connection into HTB labs?
>> openvpn

> What is the abbreviated name for a 'tunnel interface' in the output of your VPN boot-up sequence output? 
>> tun

> What tool do we use to test our connection to the target with an ICMP echo request?
>> ping

>  What is the name of the most common tool for finding open ports on a target? 
>> nmap

>  What service do we identify on port 23/tcp during our scans? 
>> telnet

Telnet is a protocol that offers remote access to machines using a virtual terminal connection however the protocol is insecure since password and text sent back and forth are not secured and are in clear text format. 
Hence this led to the development of SSH which way secure. 
Telnet is port 23 and SSH is port 22
**Ports** are basically 'doors' in a give machine that enable a user access a given service in that machine.

>  What username is able to log into the target over telnet with a blank password? 
>> root



## -------------------------| ENUMARATION PHASE |------------------------------

A tool that we usually use in enumaration more so in CTFs is usually nmap.
You can `man nmap` or use `tldr nmap` to get details about `nmap`. 
Please note that might have to install `tldr` consider the command below:
```
-$ sudo apt-get install tldr

```
After checking the tool and some of the flags/options to use with the tool, you can enter the command as follows:
```
-$ sudo nmap -p- --min-rate 5000 -sV -sC $IP -v -oN initial_scan 

---snip---

PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

---snip---

```
**`-p-`** ==> scan all the 65535 ports.
**`-sV`** ==> Enumerate the version of the service that is open.
**`-sC`** ==> Use default scrips to enumerate certain things like the version, OS
**`-v`** ==> Show me everything that the tool is doing. Being verbose.
**`-oN`** ==> On a normal file output everything there hence the filename [initial_scan]()

## ----------------------------| EXPLOITAION |---------------------------------

With what you have discovered you can opt to try and connect with the machine using telnet
```
-$ telnet $IP

Trying 10.129.141.194...
Connected to 10.129.141.194.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█

```

After this you will be required to enter login creds, so most times you always want to enter default creds like [root, admin, anonymous, ftp...]() these are usually the default usernames and passwords.

Therefore in our case:

```
Meow login: root

---snip---

root@Meow:~#

---snip---
```
Root usually defines the super user (su), other names include:
	+ admininstrator
	+ admin

```
root@Meow:~# ls
flag.txt  snap

root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19
```
> Submit root flag
>> b40abdfe23665f766f9c61ecba8a4c19
