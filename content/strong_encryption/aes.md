---
title: "AES & Padding Oracle Attack"
date: 2019-02-01

---

Under construction...

https://crypto.stackexchange.com/questions/3714/how-does-a-padding-oracle-attack-work
https://resources.infosecinstitute.com/padding-oracle-attack-2/#gref
https://tlseminar.github.io/padding-oracle/

https://thebestvpn.com/advanced-encryption-standard-aes/

https://www.youtube.com/watch?v=1sdOaXTUGa4
https://robertheaton.com/2013/07/29/padding-oracle-attack/
https://blog.skullsecurity.org/2013/padding-oracle-attacks-in-depth
https://erlend.oftedal.no/blog/poet/

x=1 ^ y=1 = 0
x=1 ^ y=0 = 1
x=0 ^ y=0 = 0
x=0 ^ y=1 = 1

An intuitive way of understanding why XOR is associative is as follows:

First recognize that XOR is commutative, that is, a⊕b=b⊕a. This can be done using a truth table.

Then, think of the XOR operator as a 'conditional flip' operator, that is think of a⊕b as saying if a is 1, take flipped b as the output, while if a is 0, take b as the output. Because of the commutative property, a⊕b is completely equivalent to b⊕a which says that b conditionally flips a.

Now consider a⊕(b⊕c). This is saying that b conditionally flips c, the result of which is itself conditionally flipped by a. Because of the nature of the flipping operation, ultimately c is conditionally flipped by both b and a, and it doesn't matter in which order these two flip operations are performed. Because of this, it is seen that a⊕(b⊕c) is equivalent to b⊕(a⊕c).

Finally, by using commutativity, one can write a⊕(b⊕c)=(b⊕c)⊕a, which was just showed to be equivalent to b⊕(a⊕c), itself equivalent to b⊕(c⊕a) by commutativity.

Therefore, (b⊕c)⊕a=b⊕(c⊕a) which proves associativity.
