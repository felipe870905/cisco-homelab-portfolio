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
