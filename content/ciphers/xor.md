---
title: "XOR Cipher"
date: 2019-01-17

---

In cryptography, the simple XOR cipher is a type of additive cipher.  

XOR is short for exclusive or. It is a bitwise operator.  
If one of the two operands, but not both are true, the statement is true.  

XOR truth table:  

![xor](xor.png)

In the following example we ask for input text and key then calculate the length of the input string and store it in n.  
Then we iterate over each byte (each ASCII character) and perform a XOR operation (k XOR b in below example) on each <b>bit</b> within the byte.  

Python indicates XOR with the ^ operator.  
In python chr(i) returns a string of one character whose ASCII code is the integer i.  

If the key is a single byte like "A", then you use the same byte to encrypt every character of the plain text. You just keep repeating it over and over (Capital A for this byte, capital A for the next byte, etc..). If the key is multi-byte, for example: "BC" then you repeat the pattern, you use capital B for the first byte, capital C for the next byte, capital B for the third byte etc.

```Shell
k = key[i%len(key)]
```
This makes sure the key stays within bounds of the length of the key by performing modulus ("clock arithmetic")  
So if the key is "qrs" the value of i within the for loop statement k = key[i%len(key)] will be either 0,1 or 2.  
This is how the pattern of the multi-byte key is repeated 'over' the text.

```python
text = raw_input("Enter text: ")
key = raw_input("Enter key: ")
n = len(text)

for i in range(n):
  t = text[i]
  k = key[i%len(key)]
  x = ord(k) ^ ord(t)
  print t, k, x, chr(x)
```

Sample in/output:  

```Shell
Enter text: HELLO
Enter key: qrs
H q 57 9
E r 55 7
L s 63 ?
L q 61 =
O r 61 =
```

To break this down for the first byte:  

```Shell
The character H in ASCII is 01001000 in binary  
The character q in ASCII is 01110001 in binary  

XORRING these bytes:  
|0|1|0|0|1|0|0|0|   
|0|1|1|1|0|0|0|1|  
|0|0|1|1|1|0|0|1|
```

00111001 is 57 in decimal and when we look up 57 in the ASCII table it is the character 9  

If you combine upper and lower case characters XOR often produces unprintable bytes.  
The best way to handle the output is not to print it with ASCII but instead print it as HEX encoded values.

```python
text = raw_input("Enter text: ")
key = raw_input("Enter key: ")
n = len(text)

cipher = ""
for i in range(n):
  t = text[i]
  k = key[i%len(key)]
  x = ord(k) ^ ord(t)
  cipher += chr(x)
print text, key, cipher.encode("hex")
```

Suppose we need to decrypt the following ciphertext: kquht} and we know the key is a single ASCII digit.  
The following code will loop threw all 10 digits and perform a XOR operation using every single digit.

```python
text = raw_input("Enter text: ")
n = len(text)

for k in "0123456789":
  clear = ""
  for i in range(n):
    t = text[i]
    x = ord(k) ^ ord(t)
    clear += chr(x)
  print k, clear
```

Output:

```Shell
0 [AEXDM
1 Z@DYEL
2 YCGZFO
3 XBF[GN
4 _EA\@I
5 ^D@]AH
6 ]GC^BK
7 \FB_CJ
8 SIMPLE <---
9 RHLQMD
```

When we have a HEX encoded ciphertext of '70155d5c45415d5011585446424c' and we know the key is 2 ASCII digits, we need to decode the encoded string first.  
Then bruteforce threw all digits from 10-99 (skipped the first range 00 - 09 because) to get the desired deciphered text. Yah \o/

```python
c = "70155d5c45415d5011585446424c"
text = raw_input("Enter text: ").decode("hex")
n = len(text)

for key in range(10,99):
    key = str(key)
    clear = ""
    for i in range(n):
      t = text[i]
      k = key[i%len(key)]
      x = ord(k) ^ ord(t)
      clear += chr(x)
    print key, clear
```

Output:

```Shell
Enter text: 70155d5c45415d5011585446424c
10 A%lltql hevs|
11 Almtpla iews}
12 Alntslb jets~
13 A&lotrlc keus
14 A!lhtuld lersx
15 A little messy  <---
```

Or a different approach:

```python
texth = raw_input("Enter hex plaintext: ")
text = texth.decode("hex")
n = len(text)

for k1 in "0123456789":
  for k2 in "0123456789":
    key = k1 + k2
    clear = ""
    for i in range(n):
      t = text[i]
      k = key[i%2]
      x = ord(k) ^ ord(t)
      clear += chr(x)
    print key, clear
```

## Why XOR is associative and commutative

* commutative = a group of quantities connected by operators gives the same result whatever the order of the quantities involved, e.g. a × b = b × a.
* associative = a group of quantities connected by operators gives the same result whatever their grouping, i.e. in whichever order the operations are performed, as long as the order of the quantities remains the same, e.g.

(1*2) * 3 = 6  
(1*3) * 2 = 6  
(2*3) * 1 = 6  

An intuitive way of understanding why XOR is associative is as follows:

First recognize that XOR is commutative, that is, a⊕b=b⊕a. This can be done using the truth table.

Then, think of the XOR operator as a 'conditional flip' operator, that is think of a⊕b as saying if a is 1, take flipped b as the output, while if a is 0, take b as the output. Because of the commutative property, a⊕b is completely equivalent to b⊕a which says that b conditionally flips a.

Now consider a⊕(b⊕c). This is saying that b conditionally flips c, the result of which is itself conditionally flipped by a. Because of the nature of the flipping operation, ultimately c is conditionally flipped by both b and a, and it doesn't matter in which order these two flip operations are performed.   
Because of this, it is seen that a⊕(b⊕c) is equivalent to b⊕(a⊕c).

Finally, by using commutativity, one can write a⊕(b⊕c)=(b⊕c)⊕a, which was just showed to be equivalent to b⊕(a⊕c), itself equivalent to b⊕(c⊕a) by commutativity.

Therefore, (b⊕c)⊕a=b⊕(c⊕a) which proves associativity.
