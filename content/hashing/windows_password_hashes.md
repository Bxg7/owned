---
title: "Windows Password Hash Cracking"
date: 2019-01-31

---

Create 3 users on Windows 2016 Server (i used evaluation version in VirtualBox)

![hash1](hash1.png)

Disable Realtime Virus Protection:

![rtp](rtp.png)

Create directory c:\mimikatz and open command prompt in this directory, then run this command to export the SAM database into the c:\mimikatz directory:

```sh
reg save HKLM\SAM sam.hiv
```

Download mimikatz from https://github.com/gentilkiwi/mimikatz/releases, extract to c:\mimikatz and run it from a command prompt:  

```sh
c:\mimikatz>mimikatz.exe
```

In the mimikatz prompt run these commands:  

```sh
log
privilege::debug
token::elevate
lsadump::sam sam.hiv
```

Output:  

```sh
RID  : 000003e8 (1000)
User : john
  Hash NTLM: 4649843ceeac228e43667d160ab1d994

RID  : 000003e9 (1001)
User : mary
  Hash NTLM: 4649843ceeac228e43667d160ab1d994

RID  : 000003ea (1002)
User : dennis
  Hash NTLM: 0342DB37D0A08A6EA2284584876CCED0
```

If you now run this command in python we can see the hash is using md4 with utf-16le encoding.  

```python
import hashlib
hashlib.new("md4","P@sw0rd".encode("utf-16le")).hexdigest()
'4649843ceeac228e43667d160ab1d994' <---- john and mary's P@sw0rd hash!
```

NTHash is a very weak and old password system:

* Encode the password in Unicode (Microsoft uses Unicode since it is an international OS, it allows passwords in languages like japanese and chinese that do not encode with 8 bits per character but 16 bits per character)
* Hash it with MD4

Grumpy cat does not approve:

![grumpy](grumpy.jpg)

## Hash cracking with Google

Since the password hashes have no variation (no salt used!) and any two users with the same password have the same hash you can use Google search engine to search for the hash that someone else might have already cracked:  

![hash2](hash2.png)

For the password that dennis uses in above example this will not work but we can just use the same bruteforce guessing attack as described in https://jwever.bitbucket.io/cryptography/hashing/md5/

Example of cracking a 7 digit password (10^7 = 10000000 passwords):

```python
import hashlib

# hash: 0342DB37D0A08A6EA2284584876CCED0

for c1 in "0123456789":
  for c2 in "0123456789":
    for c3 in "0123456789":
      for c4 in "0123456789":
        for c5 in "0123456789":
          for c6 in "0123456789":
            for c7 in "0123456789":
              p = c1 + c2 + c3 + c4 + c5 + c6 + c7
              hash = hashlib.new("md4", p.encode("utf-16le")).hexdigest()
              if hash[0:7] == "0342db3":
                print p, hash1
```

## Many round hash aka Stretching

Below is an example of finding a 3 digit password that uses 100 rounds of hashing (this is also called "stretching").  
So each password combination (000 - 999) is hashed 100 times. We are looking for this hash: c09145ad46b058fba82e4218169c7121

```python
import hashlib

alg = "md5"
nr  = 100

for ip in range(1000,2000):
  sp = str(ip)
  p = sp[1:]
  h = p
  for i in range(nr):
    h = hashlib.new(alg, h).hexdigest()
  # if h[0:3] == "c09":
    print p, h
```

Sample output:

```sh
373 277c4231b9cdabe7d3c14c9f85332c71
373 9cff18aecbbacba96547c662c8276674
373 f2a20e92aec5ec807c7fa6555c72bb57
373 6e00121a32d172d8a57c2082e12ffe58
373 f6a464a9242bc20f6a2483379ba1250b
373 3b47012a2f58f504923cd0352dd10148
373 327e65ca87b3c319a8a888d489abc206
373 cc1f489c2caa6a6dbcc0be8464c06128
373 6e77734aa2290d56b75de9f4c7a29888
373 978563412234ffa23fa96496e39b58da
373 51be797c06126d1c387816bd6206126c
373 10ca162375a312cf1351f21f521f4d87
373 a8257f887d93c8e590ac54c249169773
373 be485ad4e4fbe90dc002e3fdb0d9056a
373 c09145ad46b058fba82e4218169c7121 <----- found it
374 ad972f10e0800b49d76fed33a21f6698
374 38171c7a8c7826a100e42706cefe33b7
374 58524bcab6a0cc553608d216e2c40955
374 fe0570eb6e84e7a64dd0b8f0bc96da3b
374 3a97ed40dc7ee01c971a2b332cab5a80
374 3f027499467da942192689f23faee3fd
374 1733641dc1e97c9fa7de5ca785a0617c
374 ce31d294fd2b4eb102f377f64f57ccdf
374 3b8e2f121daab6f6032d056b596d6bb2
374 edf8aa4fc9fb4ecda01543ff04e3a100
374 91d503d119e93dbd408a813eba100905
374 38d16e9f20beb0e2496ba60c041b5d8d
374 13cba0c828ef2f9b5daa6ec6b3c2b58b
374 193968835eb538428f3f36f760b8ef36
```

Another example of a many-round-hash:  

* password is three digits, between 000 and 999
* hashed an unknown number of times with SHA-1 (no more then 5000)
* find the password from this hash: f6dc23c15a1d0c64e8d1c53cd95adc346ceeccbe

Solution:  

```python
import hashlib

alg = "sha1"
nrmax  = 5000

for ip in range(1000,2000):
  sp = str(ip)
  p = sp[1:]
  h = p
  for i in range(nrmax):
    h = hashlib.new(alg, h).hexdigest()
    if h[0:5] == "f6dc2":
      print i+1, "rounds: password =", p, "Hash = ",  h
```
