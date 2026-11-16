## IPv6 Configuration 

## Overview
In this lab I added IPv6 to my existing VLANs and router-on-a-stick setup. I gave each sub-interface an IPv6 address and subnetted with its corresponding VLAN number (I will write down those subnets below). I connected my MacBook in order to confirm that it would build its own IPv6 address automatically by using the SLAAC (Stateless Address Auto Configuration) without needing a DHPCv6 pool. 


## What I demonstrated
- Assigning IPv6 addresses to router sub-interfaces (a plain `::1` address and an EUI-64 address for VLAN 60)
- Enabling IPv6 routing so the router sends Router Advertisements
- A MacBook receiving a global IPv6 address and default gateway through SLAAC
- Matching the host's default gateway to the router's link-local address


## Cisco Equipment 
<img width="1591" height="664" alt="logical topology 2" src="https://github.com/user-attachments/assets/64ec3fb4-9eb1-4859-9068-96e591166b8a" />



| Device | Model | Role |
|---|---|---|
| Router | Cisco 1921 ISR | Router-on-a-stick, IPv6 gateway for each VLAN |
| Switches | 3x Cisco Catalyst 2960 | VLAN access and 802.1Q trunking |
| End host | MacBook Pro | SLAAC client (USB Ethernet adapter) |


## Environment 

Platform: Physical home lab 

Access: Console cable (terminal session to the router)

Existing setup: VLANs 10 through 60 with 802.1Q trunks and router-on-a-stick, already configured for IPv4

Host: MacBook Pro connected to a switch port in VLAN 40, IPv6 set to Automatically


## IPv6 Addressing Scheme 

Each VLAN gets its own /64. The 16-bit subnet ID matches the VLAN number.

| VLAN | Sub-interface | IPv6 subnet | Router address |
|---|---|---|---|
| 10 | G0/0.10 | 2001:DB8:0:`10`::/64 | 2001:DB8:0:10::1 |
| 20 | G0/0.20 | 2001:DB8:0:`20`::/64 | 2001:DB8:0:20::1 |
| 30 | G0/0.30 | 2001:DB8:0:`30`::/64 | 2001:DB8:0:30::1 |
| 40 | G0/0.40 | 2001:DB8:0:`40`::/64 | 2001:DB8:0:40::1 |
| 50 | G0/0.50 | 2001:DB8:0:`50`::/64 | 2001:DB8:0:50::1 |
| 60 | G0/0.60 | 2001:DB8:0:`60`::/64 | EUI-64 (built from router's `int g0/0` MAC address) |

`int g0/0` Mac-Address with EUI-64 is `6612.25ff.fecf.9fc0`

## Key Concepts

- Link-local (FE80::/10): automatically created on every IPv6 interface. Used only on the local link, never routed. NDP messages use it.
- Global unicast (2000::/3): the routable address, built from the prefix plus an interface ID.
- SLAAC: the router advertises the prefix, and the host builds its own address. No server and no lease table.
- RS / RA: a host asks "any routers here?" (Router Solicitation), and the router answers with the prefix and gateway info (Router Advertisement).
- DAD: Duplicate Address Detection, where a device checks that nobody is already using an address.
- EUI-64: builds the 64-bit interface ID from the MAC address (split the MAC, insert FFFE, flip the 7th bit).


## The Problem?
I didn't necessarily encounter any problems while configuring IPv6 in my homelab, but rather learned a few new things and saw it happen in real time in my equipment. 

## Why IPv6?
IPv4 uses 32-bit addresses which allows about 4.3 billion address combinations in total. Nowadays, there are a lot of new devices being created everyday (such as IoT devices) in which take up more IPv4 addresses that was originally intended to when IPv4 was first created. IPv6 uses a 128-bit address scheme in which allows about 340 undecillion addresses to be created. 


## Lab Walkthrough 
<img width="736" height="465" alt="Screenshot 2026-11-11 at 2 28 44 PM" src="https://github.com/user-attachments/assets/2f269930-a634-44bf-8152-f7f0602c8011" />

This is the IPv6 address format that I used in my lab (as you can see on the IPv6 Addressing Scheme table). 

<img width="853" height="511" alt="int g0:0 mac address" src="https://github.com/user-attachments/assets/9ded0033-703a-4687-a083-ef8bb539f401" />

`int g0/0` Mac-Address 

<img width="816" height="506" alt="Screenshot 2026-11-11 at 2 33 28 PM" src="https://github.com/user-attachments/assets/0c6622a3-232b-4e0f-be30-663de694a7f8" />

This is how I wrote the scheme and as well as calculating the EUI-64 MAC address for VLAN 60. The reason why I used `int g0/0` is because that is where the stick is created, which sends out all the traffic through each sub-interface. Sub-interfaces do not have Mac-Addresses, only PHYSICAL interfaces do. 

<img width="866" height="509" alt="eui-64 command for vlan60" src="https://github.com/user-attachments/assets/9fcf979d-9db0-4f49-8d1a-53ac217e56e7" />

Using the EUI-64 command. 

<img width="856" height="511" alt="eui-64 confirmed" src="https://github.com/user-attachments/assets/eb56ddfd-648d-4b93-acf7-a9103a96bd76" />

As you can see, the EUI-64 matches the one I calculated on my paper for sub-interface `0/0.60`. 

<img width="857" height="509" alt="ipv6 unicast-routing command" src="https://github.com/user-attachments/assets/4896980b-1742-4390-9a9d-cf34ce92ca9a" />

I realized that you can use the IPv6 configuration commands in order to set each address on each sub-interface, but routing will not be possible between VLANs, nor my MacBook will receive the network prefix since no Router Advertisements (RAs) would be sent. 


## Obtaining an IPv6 Address for my MacBook using VLAN 40
<img width="848" height="508" alt="checking middle switch int f0:13" src="https://github.com/user-attachments/assets/383a948f-2ad9-47ba-9dfd-a748f517f048" />

Checking to see that I connected successfully on my MiddleSwitch's `int f0/13` which falls under VLAN 40. 


<img width="897" height="508" alt="chceking the ipv6 config on my mac" src="https://github.com/user-attachments/assets/0761eb59-b155-4f43-be4f-02d7f71424f3" />

<img width="858" height="507" alt="checking the link local address " src="https://github.com/user-attachments/assets/89deecad-df45-4b96-87fc-19895b706b98" />


As you can see, my MacBook has obtained the link-local address from the router and has also generated two IPv6 addresses (macOS makes a stable one that stays on this network, and a temporary one that changes over time). All link-local addresses are the same since it uses the physical interface of `g0/0`. 

<img width="903" height="507" alt="network prefix" src="https://github.com/user-attachments/assets/315f2871-e89c-421a-9bfd-fc9212f91b4c" />

This is the network prefix and you can also see the subnet ID of 40 being read successfully by my MacBook (highlighted in blue).

<img width="899" height="505" alt="interface id " src="https://github.com/user-attachments/assets/7508fcf2-f81c-4cdd-8da2-03d0fd7250ae" />

This is the interface ID that my MacBook randomly generates due to privacy features, that way my MAC address does not get exposed. 

That is all for my IPv6 configuration lab!

