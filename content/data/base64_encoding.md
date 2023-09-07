---
title: "Base64"
date: 2019-01-16
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---
Base-64 encoding is a way encoding binary data into ASCII text so that it's more easily transmitted in things like e-mail and HTML form data.  
Each Base64 digit represents exactly 6 bits of data. Three 8-bit bytes (i.e., a total of 24 bits) can therefore be represented by four 6-bit Base64 digits.  

![Base64_5](base64_5.png)

If you have an ASCI string of 3 characters (=3 bytes) it turns into 24 bits, interpreted as 3 groups of 8 bits.  
If you then break them up into 4 groups of 6 bits you now have 4 numbers within 0 and 63.  

![Base64_4](base64_4.png)

Then use the lookup table below.  

![Base64_1](base64_1.png)

If you have something other then 3 bytes, for example say you have 4 bytes of data for the input, then base64 end with two equal signs (==) to indicate it had to add two characters of padding. If you have 5 bytes you have 1 equal sign. If you have 6 bytes there are no equal signs, indicating the input fits neatly into base64 with no need for padding.  

Base64 is not encrypting, it is not hiding anything. It is just another way to represent data.

If for example you would like to email someone an executable you could send the encoded binary data:

```python
with open ("file.exe","rb") as f:
  data = f.read()
  print data.encode("base64")

ADeFhAAIAAAAAAAAAAAAAAAqB0AAAAAAABJEigA\  
nAAAAAIcEAAAAAAAA9F0IACA4HgAs7gIACcADAP2  
VAQDgHgAAqB0AAAAAAABJEigAAAAAAIcEAAAA\  
nAAAAqB0AAAAAAADRFigAAAAAAMcEAAAAAAAAApYB  
ACywAADEzygAIAAAAAAAAAAAAAAAqB0AAAAA\nAAC  
```

Or decoding:

```python
  data = "VGhpcyBpcyB0b28gZWFzeQ=="
  data.decode("base64")
'This is too easy'
```

Example of decoding something that has been encoded several times:  

```python
#!/usr/bin/env python
import base64

a = 'VkVjNWRtRXpUV2RhTWpsMldrRTlQUT09'

def decodeit(data):    
    return data.decode("base64")

while True:
    try:
        a = decodeit(a)
        print(a)
    # we probably found the solution if we get a padding error so exit
    except:
        exit()
```

Output would be:

```sh
VEc5dmEzTWdaMjl2WkE9PQ==
TG9va3MgZ29vZA==
Looks good
```

More details: http://www.hcidata.info/base64.htm
