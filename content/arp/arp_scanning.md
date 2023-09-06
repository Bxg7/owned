---
title: "Network scanning using arp"
date: 2019-01-06
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

We can scan the LAN by sending an ARP request to the broadcast mac address iterating over every IP we specify.  
When a device sends a packet to the broadcast MAC address (FF:FF:FF:FF:FF:FF), it is delivered to all stations on the local network.  

Arp request says: "who has 192.168.0.10?"  
If a host with ip 192.168.0.10 is online it will reply with an ARP reply message saying: "I have 192.168.0.10, my mac address is 00:22:33:44:55:66"

The arp table on every host contains a list of all the IP address to MAC address mappings.  


```python
import scapy.all as scapy
import argparse


def get_arguments():
    parser = argparse.ArgumentParser()
    parser.add_argument("-t", "--target", dest="target", help="Target IP / IP Range")
    options = parser.parse_args()
    if not options.target:
        parser.error("[-] Please specify an ip range, use --help for more info.")
    return options


def scan(ip):
    arp_req = scapy.ARP(pdst=ip)
    broadcast = scapy.Ether(dst="ff:ff:ff:ff:ff:ff")
    arp_req_broadcast = broadcast/arp_req
    answered_list = scapy.srp(arp_req_broadcast, timeout=1, verbose=False)[0]

    clients_list = []

    # creating a list[] of dictionaries {}
    for element in answered_list:
        client_dict = {"ip": element[1].psrc, "mac": element[1].hwsrc}
        clients_list.append(client_dict)
    return clients_list


def print_result(results_list):
    print("IP\t\t\tMAC ADRESS\n------------------------------------------")
    for client in results_list:
        print(client["ip"] + "\t\t" + client["mac"])


options = get_arguments()
scan_result = scan(options.target)
print_result(scan_result)

```
