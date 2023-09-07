---
title: "Caesar Cipher & ROT13"
date: 2019-01-16

---

## Caesar Cipher

The Caesar cipher is one of the earliest known and simplest ciphers.  
It is a type of substitution cipher in which each letter in the plaintext is 'shifted' 3 numbers of places down the alphabet.  

![caesar](caesar1.png)

In python we first define an alphabet string. When the ask for user input. We store the length of the user input into the n variable. We then iterate over each character in the string and find its location within the alphabet string and store it in the loc variable. Then we make the shift happen by
```mk
newloc = (loc + shift) % 26
```
We then look up the shifted value in the alphabet and add it to str_out

```python
#!/usr/bin/env python
alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

str_in = raw_input("Enter text in capitals: ")
shift = 3
n = len(str_in)
str_out = ""
print "i " + "c " + "l " "nl " + "out "  

for i in range(n):
    c = str_in[i]
    loc = alphabet.find(c)
    print i, c, loc,
    # use modulus 26 since we have 26 characters in the alphabet
    newloc = (loc + shift) % 26
    str_out += alphabet[newloc]
    print newloc, str_out

print 'Obfuscated version:', str_out
```

The problem with Caesar cipher is that the keyspace is very small: {1, 2, ... 26}

## ROT13

ROT13 is the same caesar cipher code like above with a shift value 13. If you run one cycle of ROT13 and then run it again on the result of the first cycle it decrypts itself. 26 / 2 = 13. So HELLO becomes URYYB and running ROT13 on URYYB results in HELLO.  

This is an important feature because there are quite a few cryptographic functions that have this property. Where you encrypt something once and when you encrypt it again you reverse the process, instead of becoming more encrypted it becomes unencrypted.

## Bruteforce attack

To bruteforce you're way threw all possible shift values:

```python
alpha =  "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
str_in = raw_input("Enter ciphertext: ")


for shift in range(26):    
    n = len(str_in)
    str_out = ""

    for i in range(n):
       c = str_in[i]
       loc = alpha.find(c)
       newloc = (loc + shift)%26
       str_out += alpha[newloc]

    print shift, str_out
```

Output:

```sh
Enter ciphertext: MYXQBKDEVKDSYXC
0 MYXQBKDEVKDSYXC
1 NZYRCLEFWLETZYD
2 OAZSDMFGXMFUAZE
3 PBATENGHYNGVBAF
4 QCBUFOHIZOHWCBG
5 RDCVGPIJAPIXDCH
6 SEDWHQJKBQJYEDI
7 TFEXIRKLCRKZFEJ
8 UGFYJSLMDSLAGFK
9 VHGZKTMNETMBHGL
10 WIHALUNOFUNCIHM
11 XJIBMVOPGVODJIN
12 YKJCNWPQHWPEKJO
13 ZLKDOXQRIXQFLKP
14 AMLEPYRSJYRGMLQ
15 BNMFQZSTKZSHNMR
16 CONGRATULATIONS  <------ :)
17 DPOHSBUVMBUJPOT
18 EQPITCVWNCVKQPU
19 FRQJUDWXODWLRQV
20 GSRKVEXYPEXMSRW
21 HTSLWFYZQFYNTSX
22 IUTMXGZARGZOUTY
23 JVUNYHABSHAPVUZ
24 KWVOZIBCTIBQWVA
25 LXWPAJCDUJCRXWB
26 MYXQBKDEVKDSYXC
```

---
