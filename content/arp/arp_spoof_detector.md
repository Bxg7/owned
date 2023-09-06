---
title: "ARP spoof detection"
date: 2019-01-10
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

In order to detect if we are under attack we can use following code. It will check if the MAC address (.hwsrc field in ARP reply packet) that is associated with the spoofed IP address that the attacker is sending with the packet (.psrc field in ARP reply packet) differs from what we get back (=real mac address) when we run get_mac function.  

Some words on defense: https://security.stackexchange.com/questions/161173/how-exactly-do-you-protect-against-arp-spoofing-in-large-lans

```python
#!/usr/bin/env python
import scapy.all as scapy_packet

def get_mac(ip):
    arp_request = scapy.ARP(pdst=ip)
    broadcast = scapy.Ether(dst="ff:ff:ff:ff:ff:ff")
    arp_request_broadcast = broadcast/arp_request
    answered_list = scapy.srp(arp_request_broadcast, timeout=1,
                              verbose=False)[0]
    if answered_list:
        return answered_list[0][1].hwsrc
    else:
        return None

def sniff(interface):
  scapy.sniff(iface=interface, store=False, prn=process_sniffed_packet)

def process_sniffed_packet(packet):
  if packet.haslayer(scapy.ARP) and packet[scapy.ARP].op == 2:
    try:
      real_mac = get_mac(packet[scapy.ARP].psrc)
      response_mac = packet[scapy.ARP].hwsrc

      if real_mac != response_mac:
        print("[+] Arp spoof attack detected!")
    except IndexError:
      password

sniff("enp7s0")
```
