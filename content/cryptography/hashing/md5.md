---
title: "MD5 & SHA Hashes"
date: 2019-01-30

---

## What is a hash?

* A hash is a way to make a 'fingerprint' of a file
* You take all the bytes of a file and combine them together using a hashing algorithm
* This creates a fixed length hash value
* Changing any part of the file will result in a completely different hash value when recalculating
* So when you have two files that are supposed to be identical, you can verify this by comparing the hashes.

## MD5

* MD5 is a very commonly used hash, it is 128 bits long which is considered to be short for a hash function
* It is reliable enough for most purposes, ie: its being used for putting fingerprints on downloads or malware samples
* It is sometimes used to obscure passwords.
* It is not a perfectly secure hashing function, collisions are possible, so you cannot be 100% certain two files are identical, but usually they are.
* Its easy to calculate MD5 in python:

```python
import hashlib
hashlib.new("md5", "sometext").hexdigest()
'a29e90948f4eee52168fab5fa9cfbcf8'
```

The function hexdigest() returns a hexadecimal string representation of the hash.  
Since there are 32 hexadecimal characters in this output string and each character is 4 bits ('a nibble') long. 32 * 4 = 128 bits.

## SHA-1, SHA-2, and SHA-3

* The Secure Hash Algorithm was designed to be an improvement on MD5.
* SHA-1 had no collisions untill sometime ago when researchers at Google found out how to make collisions in SHA-1.
* SHA-2 is considered to be the secure successor.
* SHA-2 is expected to remain secure for a very long time.
* There is also SHA-3 but it is not being used much.
* The most common used lengths are 256 or 512 bits.
* Below are examples of SHA-1 and SHA-2 (256 & 512) creation.

```python
hashlib.new("sha1","sometext").hexdigest()
'd22a158c8ead99dbd7eddb86104496f3ee087049'
hashlib.new("sha256","sometext").hexdigest()
'5fb2054478353fd8d514056d1745b3a9eef066deadda4b90967af7ca65ce6505'
hashlib.new("sha512","sometext").hexdigest()
'5bcca117c56a831c0a5b6c14c4bf3b16e10c610ca79f51165e899fd6267639e4333c8a1f973688d7c88b3950f58961d75bc5d4036ded30a10fb6432317abdd8a'
```

* There is no algorithm to reverse hashes
* What you can do is guess the data and hope you find a match
* As an example, suppose you have the following hash: db0edd04aaac4506f7edab03ac855d56
* You could use a password list, calculate the hash for each password in that list and compare the result to your hash.
* Since MD5 and the SHA family are designed to calculate hashes very fast you could potentially calculate millions of passwords per second.

```python
import hashlib

for p in "1234567890":
    guess = "password" + p
    h = hashlib.new("md5", guess).hexdigest()
    print guess, h

```

Output:

```sh
password1 7c6a180b36896a0a8c02787eeafb0e4c
password2 6cb75f652a9b52798eb6cf2201057c73
password3 819b0643d6b89dc9b579fdfc9094f28e
password4 34cc93ece0ba9e3f6f235d4af979b16c
password5 db0edd04aaac4506f7edab03ac855d56   <---- we got a match, the password is 'password5'
password6 218dd27aebeccecae69ad8408d9a36bf
password7 00cdb7bb942cf6b290ceb97d6aca64a3
password8 b25ef06be3b6948c0bc431da46c2c738
password9 5d69dd95ac183c9643780ed7027d128a
password0 305e4f55ce823e111a46a9d500bcb86c
```
