# Cisco Home Lab Portfolio

Welcome to my hands-on networking portfolio!

I'm Felipe, an IT graduate with an AAS Network and Computer Administrator Degree from Lone Star College - North Harris. I currently hold CCNA, CompTIA Security+, and LPI Linux Essentials certifications. I'm looking for an entry-level networking role/help desk role in the Houston area in order to get started in my career. 

## About This Repository 
Every lab that I complete in this project is a review of a topic that I already studied and I am just trying to refresh upon them in my physical lab. For each lab, I document what I configured, what went wrong, how I diagnosed it, the root cause, and what I learned. 

*Majority of these labs were already recorded and uploaded on my TikTok networking page in which I will attach the link at the bottom of the page! (Note: those videos could extend to over 20 minutes of showtime so it is best to check it out here first.)*


## What My Physical Lab Consists Of
<img width="1591" height="664" alt="logical topology 2" src="https://github.com/user-attachments/assets/cfba9f9d-a83b-4e4f-84c4-df9364238301" />

- 3x Cisco Catalyst 2960 Switches (Top Switch, Middle Switch, and Bottom Switch)
- 1x Cisco 1921 ISR Router (router-on-a-stick, DHCP server)
- 6 VLANs on a custom made VLSM scheme using `192.168.1.0/24` IP address range
- Console access over serial
- WireShark for Packet Capture analysis (for some labs)


## Subnetting Info 
VLAN 10: VideoGameDevices
- Network: `192.168.1.144/28` (mask 255.255.255.240)
- Router gateway: `192.168.1.145`
- Usable hosts: `.145` to `.158` (14 total)
- Broadcast: `.159`

VLAN 20: SecurityCameras
- Network: `192.168.1.96/27` (mask 255.255.255.224)
- Router gateway: `192.168.1.97`
- Usable hosts: `.97` to `.126` (30 total)
- Broadcast: `.127`

VLAN 30: TVs
- Network: `192.168.1.160/29` (mask 255.255.255.248)
- Router gateway: `192.168.1.161`
- Usable hosts: `.161` to `.166` (6 total)
- Broadcast: `.167`

VLAN 40: SmartBoards
- Network: `192.168.1.64/27` (mask 255.255.255.224)
- Router gateway: `192.168.1.65`
- Usable hosts: `.65` to `.94` (30 total)
- Broadcast: `.95`

VLAN 50: VOIPhones
- Network: `192.168.1.0/26` (mask 255.255.255.192)
- Router gateway: `192.168.1.1`
- Usable hosts: `.1` to `.62` (62 total)
- Broadcast: `.63`

VLAN 60: Printers & Scanners
- Network: `192.168.1.128/28` (mask 255.255.255.240)
- Router gateway: `192.168.1.129`
- Usable hosts: `.129` to `.142` (14 total)
- Broadcast: `.143`


## Technologies and Skills
- Cisco IOS
- VLANs and 802.1Q Trunking
- Inter-VLAN Routing (Router-on-a-Stick)
- DHCP (per-VLAN pools)
- Spanning Tree Protocol (STP): PortFast, BPDU Guard, and BPDU Filter
- EtherChannel (PAgP)
- Port Security and err-disable recovery
- Packet Analysis with WireShark

## Disclaimer
<mark>There might be some stuff I might've missed on the labs I completed previously as I am mostly doing them by reviewing my notes/what I know from the top of my head. I will try with the best of my ability to complete them with all of the information from within that topic.<mark>

## Future Labs
- IPv6 Configuration & Subnetting
- Purchase a Cisco Firewall with built-in VPN
- Purchase another Cisco 1921 Router in order to complete VRRP (Virtual Router Redundancy Protocol) and OSPF.

## TikTok Networking Page
https://www.tiktok.com/@networkingwithcruz?is_from_webapp=1&sender_device=pc



