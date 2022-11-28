---
layout: post
title: "Crack Hashes Walkthrough THM"
categories:
     - Try Hack Me
     - Easy
---

### What is hashing?

This is the process of transforming any given key or a string of characters into another value. Many encryption algorithms use hashing to enhance cybersecurity. Hashed strings and inputs are meaningless to hackers without a decryption key.


**[Fun fact]** Linux uses the yescrypt as the default hash function.

Hashes usually use a hexadecimal or base64 charset. So in order to visualize this hash concept I want you to think about your password.
When you enter a password in any login page what happens is that depending the hash algorithm/function used in that page the plain text password that you entered gets hashed into a hash value and that hash value is compared to the stored hash value of your password that you entered when you first created the password.

Now you see why storing passwords in a hash format is invaluable.

Futhermore, when we talk about cracking a hash value, what we mean is basically using a wordlist of know plain text passwords and hashing those passwords with a given hash mode or type and comparing it to the hash value we are trying to crack.  
 
Anywho, there are varieties of hash types and therefore the best way to get started with determining the hash type is using the terminal tools like:

	1. [🔥️] hash-identifier
	2. [🔥️] hashid (the hashid is way better in my opinion.)
	
```
- $ hash-identifier 48bb6e862e54f2a795ffc4e541caed4d

---snip---

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

---snip---
```

```
- $ hashid 48bb6e862e54f2a795ffc4e541caed4d

Analyzing '48bb6e862e54f2a795ffc4e541caed4d'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 

---snip---

```
As you can see it is prudent to use both to fully verify the hash ids.

After determining the hash type you can opt to use online tools like to crack the hashes:

1. [crackstation](https://www.crackstation.net)
2. [hashkiller](https://www.hashkiller.co.uk)
3. [hashes](https://hashes.com)

However, if you are more adventurous you can opt to use terminal tools like `hashcat` and `johntheripper`.
In my case I getting more faamiliar with hashcat, nevertheless, in future I'll practise with johntheripper.

`TIP` One thing I picked up with hashcat is once a hash is cracked the info get stored in a potfile and if you try to use hashcat to crack hash once more it won't get executed.

```
-$ hashcat -a 0 -m 0 482c811da5d5b4bc6d497ffa98491e38 Desktop/rockyou.txt -r Desktop/best64.rule

---snip---

INFO: All hashes found as potfile and/or empty entries! Use --show to display them.

---snip---

```
This is what am talking about 👆️.
Hence when you want to view the cracked hash output you need to use the `--show` flag also remember to specifiy the hash-mode/hash type.

```
─$ hashcat --show 482c811da5d5b4bc6d497ffa98491e38 -m 0
482c811da5d5b4bc6d497ffa98491e38:password123
```

Also you can use this [site]( https://hashcat.net/wiki/doku.php?id=example_hashes) to get the mode of a hash or use the man pages `man hashcat`	 to determine the mode.
Better still you can use `hashid` with the flag `-m` to have the hashcat mode, example.
```
-$ hashid -m  48bb6e862e54f2a795ffc4e541caed4d

---snip---

[+] MD5 [Hashcat Mode: 0]
[+] MD4 [Hashcat Mode: 900]

---snip---
```

Now, below are the answers to the THM challange called [**Crack the hash**](https://tryhackme.com/room/crackthehash)

> 48bb6e862e54f2a795ffc4e541caed4d

```
─$ hash-identifier  48bb6e862e54f2a795ffc4e541caed4d

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
	
-$ hashcat -a 0 -m 0 48bb6e862e54f2a795ffc4e541caed4d rockyou.txt -r best64.rule

---snip---

48bb6e862e54f2a795ffc4e541caed4d:easy

---snip---
```
`-a` is the attack mode and 0 represents straight.
`-m` describes the hash type/mode and 0 represents MD5

> CBFDAC6008F9CAB4083784CBD1874F76618D2A97

```
─$ hash-identifier CBFDAC6008F9CAB4083784CBD1874F76618D2A97

Possible Hashs:
[+] SHA-1
[+] MySQL5 - SHA-1(SHA-1($pass))

─$ hashcat -a 0 -m 100 CBFDAC6008F9CAB4083784CBD1874F76618D2A97 rockyou.txt -r best64.rule

cbfdac6008f9cab4083784cbd1874f76618d2a97:password123
```

> 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

```
─$ hash-identifier 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

Possible Hashs:
[+] SHA-256
[+] Haval-256

─$ hashcat -a 0 -m 1400 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032 rockyou.txt -r best64.rule

1c8bfe8f801d79745c4631d09fff36c82aa37fc4cce4fc946683d7b336b63032:letmein

```

> $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

Here I had trouble identifying the type of hash in use. 

```
-$ hash-identifier $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

---snip---

 Not Found.
 
---snip---

-$ hashid $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

[+] Unknown hash
```

As you can see it got a bit frustrating. Fortunately, after some googling I found that I have to use `\` infront of the `$` in order for it to be ignored and not to be read directly.Also you need to use `""` on the hash.
This also applies when using [hashcat]().
So it turns out that if a hash has dollar signs `$` in it, this is usually a delimiter between the salt and the hash. 

But even before coming to that conclusion I used [hashes.com](https://hashes.com) to determing the hash type and this is what I found.

> $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom - Possible algorithms: bcrypt $2*$, Blowfish (Unix), bcrypt(md5($plaintext))

Going back to the terminal.

```
─$ hashid -m "\$2y\$12\$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom"                                              
Analyzing '$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom'
[+] Blowfish(OpenBSD) [Hashcat Mode: 3200]
[+] Woltlab Burning Board 4.x 
[+] bcrypt [Hashcat Mode: 3200]


─$ hashcat -m 3200 "\$2y\$12\$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom" rockyou.txt 

$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom:bleh:3200
```
> 279412f945939ba78ce0758d3fd83daa

```
─$ hash-identifier 279412f945939ba78ce0758d3fd83daa

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

─$ hashcat -a 0 -m 900 279412f945939ba78ce0758d3fd83daa rockyou.txt -r best64.rule

279412f945939ba78ce0758d3fd83daa:Eternity22

279412f945939ba78ce0758d3fd83daa:457465726e6974793232:900
```
In the above we got 2 results the last result is a hex value when you `unhex` it you should get [Eternity22]()

> F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

```
─$ hash-identifier F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

Possible Hashs:
[+] SHA-256
[+] Haval-256


─$ hashcat -a 0 -m 1400 F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85 rockyou.txt -r best64.rule 


f09edcb1fcefc6dfb23dc3505a882655ff77375ed8aa2d1c13f640fccc2d0c85:paule

```

> 1DFECA0C002AE40B8619ECF94819CC1B

```
─$ hash-identifier 1DFECA0C002AE40B8619ECF94819CC1B 

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

-$ hashcat -a 0 -m 1000 1DFECA0C002AE40B8619ECF94819CC1B rockyou.txt -r best64.rule 

1dfeca0c002ae40b8619ecf94819cc1b:n63umy8lkf4i
```

> $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.
>> Salt: aReallyHardSalt

One really interesting thing was the salt bit since upto to this point I hadn't encountered such 😅️.

Here I used the hash identifier through hashes.com and found out below.
```
Salt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02. - Possible algorithms: sha512crypt $6$, SHA512 (Unix)
```

When using the terminal tools here is what I found.

```
─$ hashid -m  "\$6\$aReallyHardSalt\$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02."

Analyzing '$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.'
[+] SHA-512 Crypt [Hashcat Mode: 1800]
```

The challenge now came in cracking this particular hash.

```
─$ hashcat -m 1800 "\$6\$aReallyHardSalt\$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02." rockyou.txt -r best64.rule

$6\$aReallyHardSalt\$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:waka99
```
Please note for the above it will take you some time to crack it 😐️.

> e5d8870e5bdd26602cab8dbe07a942c8669e56d6
>>  tryhackme

When I tried with hash-identifier I was unable to pick out any definitive hash more so since the challenge had a hint as you can see below.

```
─$ hash-identifier e5d8870e5bdd26602cab8dbe07a942c8669e56d6

Possible Hashs:
[+] SHA-1
[+] MySQL5 - SHA-1(SHA-1($pass))
```

Therefore, I used `hashid` and found.

```
─$ hashid -m e5d8870e5bdd26602cab8dbe07a942c8669e56d6                                                                 
Analyzing 'e5d8870e5bdd26602cab8dbe07a942c8669e56d6'
[+] SHA-1 [Hashcat Mode: 100]
[+] Double SHA-1 [Hashcat Mode: 4500]
[+] RIPEMD-160 [Hashcat Mode: 6000]
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn [Hashcat Mode: 190]
[+] Skein-256(160) 
[+] Skein-512(160)
```

After determining the hash type then we crack it using hashcat.

```
─$ hashcat -m 160 e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme rockyou.txt -r best64.rule

e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme:481616481616
```

This was quite something!



	


