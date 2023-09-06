---
title: "ASCII"
date: 2019-01-09
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

![Test](more-menu.png)


* Character set 
* Often used to represent english language characters.  
* Originally it was 7-bit binary, which gave us 128 unique characters.
* The current standard is 8-bit (1 byte) or equivalently 2 hex digits per character.

Computers can only understand numbers, so an ASCII code is the numerical representation of a character such as 'a' or '1'.

![asci1](/img/asci.png)

Example:

 * character "1"
 * hex 0x31  
 * first nibble = 3 which is 0011 in binary (count the weight of the columns: 84<b>21</b>)  
 * second nibble = 1 = which is 0001 in binary (count the weight of the columns: 842<b>1</b>)  
 * The full byte binary value is 0011 0001 which is 49 in decimal (32+16+1)

The key thing to remember: the character "1" is NOT the same thing as the number 1.   

So if you have a character "1" in a file, it is not stored as a single bit 1.    
It is stored as the byte 0x31 that can be looked up in the table.  
