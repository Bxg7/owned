---
title: "Linux Password Hash Cracking"
date: 2019-01-31

---

Create 3 users in Linux and set their passwords:  

```mk
sudo useradd -r -s /bin/false john
sudo useradd -r -s /bin/false mary
sudo useradd -r -s /bin/false dennis
sudo passwd john (using P@ssw0rd as password)
sudo passwd mary (using P@ssw0rd as password)
sudo passwd dennis (using P@ssw0rd999 as password)
```

The /etc/shadow contents:

```mk
sudo cat /etc/shadow | grep -E 'john|mary|dennis'
john:$6$WvpWG7ls$IDqt8f/xBMC8h7IB57pWrQ3o06MATJjby7wkF2hqKN2y4k7HgPg7yCknlM.lSaftV4UI0ZauJnL.RWVOGD.jb.:17927::::::
mary:$6$pwiGPdMP$CvjOQZPsnAaWVRwWcOjimPWdNzucgCIh6DAVWF5io0zn0hzjWJPkVDpUX6OryYaOahRu4t5TxMXgXEqPkIfRK1:17927::::::
dennis:$6$nJO6qY4r$YfG8AWh22sncAusvM..92uzphXtnbEej1rpyP0yrYSQlI7XATA9PEknEb52PVHxXH5C/Oxo46ssMEwZG6PGJp1:17927::::::
```

After each username comes $6 which indicates this is a password type 6, meaning its hashed using SHA-512, which is the most modern and secure form of password hashing. SHA-256 password hashes should begin with $5. After the $6 we get another $ sign indicating what comes next is the SALT. Then comes another $ sign followed by the actual password hash.

For user john that would be:  

```sh
SALT: WvpWG7ls
HASH: IDqt8f/xBMC8h7IB57pWrQ3o06MATJjby7wkF2hqKN2y4k7HgPg7yCknlM.lSaftV4UI0ZauJnL.RWVOGD.jb.
```

Some things to observe:

* the password hash is much longer then a windows password hash and more complicated
* even though john and mary have the same password they have completely different hashes because a random SALT is added to each one before hashing in order to obscure the fact that those password are the same. Salting makes the password much stronger.  
* linux password hashes use both Salting (add random characters before hashing) & Stretching (hash multiple times).  
* instead of simply using one round of md4 it uses 5000 rounds of SHA-512.  
* this means it takes much more CPU time to calculate the hash in order to slow down attackers.
* the Salt and the hash are Base64-encoded.  

The details of this hashing algorithm can be found in the /etc/login.defs file.  

To use in python:
```sh
pip install passlib
```

To recreate john's hash:

```python
sha512_crypt.using(salt="WvpWG7ls", rounds=5000).hash("P@ssw0rd")
'$6$WvpWG7ls$IDqt8f/xBMC8h7IB57pWrQ3o06MATJjby7wkF2hqKN2y4k7HgPg7yCknlM.lSaftV4UI0ZauJnL.RWVOGD.jb.'
```

So to attack this we can use a bruteforce dictionary guessing attack, eventually finding the matching hash.

```python
sha512_crypt.using(salt="WvpWG7ls", rounds=5000).hash("P@ssw0ra")
'$6$WvpWG7ls$fC.NzVa0wZwWAcMdNKFi0iJhif9xhgcyiSMUfrBcera7x9UlSR.N33.A5d1wunLJuuJ74d65qHJD7SnC162tQ/'
sha512_crypt.using(salt="WvpWG7ls", rounds=5000).hash("P@ssw0rb")
'$6$WvpWG7ls$2tXaVYseB.oYkxLKDg5zz0JYpQud0SwIy.a/.3wK350Ebs4dfakhYcnVuSUlOOuIivSEOTCZWgDJq.luxvTKg0'
sha512_crypt.using(salt="WvpWG7ls", rounds=5000).hash("P@ssw0rc")
'$6$WvpWG7ls$XaEhKjRj2R1JZWCNxKUjLfHUmNmYPgn54SKcBIvUgZsy/f74J.y.RFkGrN69J6i5Liq82/7dt5Qy.yGEBXnYt1'
sha512_crypt.using(salt="WvpWG7ls", rounds=5000).hash("P@ssw0rd")
'$6$WvpWG7ls$IDqt8f/xBMC8h7IB57pWrQ3o06MATJjby7wkF2hqKN2y4k7HgPg7yCknlM.lSaftV4UI0ZauJnL.RWVOGD.jb.' <--- match
```

Suppose we have the following salt / hash:

```sh
SALT: PcSLfisbduUgOzRBUGOyr4OghBm8JRfNTDnbo5sMbrFP/wgT816rNwGRDcZdkQ/CeejGgp9eiHB17v/zZ/HqT/
HASH: penguins
```

And we know the password is 3 digits long, we can now do:  

```python
from passlib.hash import sha512_crypt
s = 'penguins'

for ip in range(1000,2000):
  sp = str(ip)
  p = sp[1:]  
  h = sha512_crypt.using(salt=s, rounds=5000).hash(p)
  if h[12:16] == "PcSL":
    print 'The password for hash ' + h[12:] + ' with SALT ' + s + ' is: ' + p
```

Output:

```mak
The password for hash PcSLfisbduUgOzRBUGOyr4OghBm8JRfNTDnbo5sMbrFP/wgT816rNwGRDcZdkQ/CeejGgp9eiHB17v/zZ/HqT/ with SALT penguins is: 826
```
