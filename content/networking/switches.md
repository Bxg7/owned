---
title: "L2 vs L3 switch"
date: 2019-01-13

---

When you ping across (so when you go from one interface to another/from one subnet to another) a router or a layer 3 switch, the layer 2 information is updated at each HOP (meaning the layer 2 headers are updated).
The layer 3 information remains the same unless NAT is used.

When you move from one VLAN to another VLAN on a layer3 switch, or move from one interface to another interface on a router the layer3 information is not changed
but the layer 2 headers are rewritten.

A router is a layer3 device, it makes routing deciscion based on IP addresses and it rewrites MAC addresses. Layer3 switches also operate at this layer.

Traditional switching operates at layer 2 of the OSI model, where packets are sent to a specific switch port based on destination MAC addresses. Routing operates at layer 3, where packets are sent to a specific next-hop IP address, based on destination IP address. Devices in the same layer 2 segment do not need routing to reach local peers. What is needed however is the destination MAC address which can be resolved through the Address Resolution Protocol (ARP) as illustrated below:  

![S1](s1.png)

Here, PC A wants to send traffic to PC B at IP address 192.168.1.6.  It does not know the unique MAC address however, until it discovers it through an ARP, which is broadcasted throughout the layer 2 segment:  

![S1](s2.png)  

It then sends the packet to the appropriate destination MAC address which the switch will then forward out the correct port based on its MAC-Address-Table.  

Within a layer 2 switch environment exists a broadcast domain.  Any broadcast traffic on a switch will be forwarded out all ports with the exception of the port the broadcast packet arrived on.  Broadcasts are contained in the same layer 2 segment, as they do not traverse past a layer 3 boundary.  

Large layer 2 broadcast domains can be susceptible to certain unintended problems, such as broadcast storms, which have the ability to cause network outages.  Also, it may be preferable to separate certain clients into different broadcast domains for security and policy reasons.  This is when it becomes useful to configure VLANs.  A layer 2 switch can assign VLANs to specific switch ports, which in turn are in different layer 3 subnets, and therefore in different broadcast domains.  VLANs allow for greater flexibility by allowing different layer 3 networks to be sharing the same layer 2 infrastructure.  The image below shows an example of a multi-VLAN environment on a layer 2 switch:  

![S1](s3.png)  

Since VLANs exist in their own layer 3 subnet, routing will need to occur for traffic to flow in between VLANs.  This is where a layer 3 switch can be utilized.  A Layer 3 switch is basically a switch that can perform routing functions in addition to switching.  A client computer requires a default gateway for layer 3 connectivity to remote subnets.  When the computer sends traffic to another subnet, the destination MAC address in the packet will be that of the default gateway, which will then accept the packet at layer 2, and proceed to route the traffic to the appropriate destination based on its routing table.  

The diagram below shows an example of a layer 3 switching routing between VLANs through its two VLAN interfaces. As before, the layer 3 device will still need to resolve the MAC address of PC B through an ARP request broadcasted out to VLAN 20.  It then rewrites the appropriate destination MAC address and forwards the packet back out the layer 2 segment:  

![S1](s4.jpg)  


When packets move from one VLAN to another VLAN on a layer3 switch, or move from one interface to another interface the layer3 information is unchanged but the layer 2 headers are rewritten. The layer 3 information remains the same unless NAT is used.  
