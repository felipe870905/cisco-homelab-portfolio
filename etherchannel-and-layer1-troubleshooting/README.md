## EtherChannel / Layer 1 Troubleshooting 

## Overview 
EtherChannel bundles several physical links into one logical link in a Cisco switch. What it helps the switch spread the traffic across all physical links, even if one physical link goes down, the EtherChannel still works. 

## Note to Audience 
This post is not a walkthrough of what I did in order to create the EtherChannel but rather solving a bundling problem. 

## Environment 
<img width="1591" height="664" alt="logical topology 2" src="https://github.com/user-attachments/assets/b9a72494-a751-4894-b139-d2221ed1677c" /> 


*No configurations are done in the Packet Tracer topology as the picture itself is only used as a reference of what my environment looks like*


- 2 EtherChannel bundles across all 3 switches (Po1 and Po2 are labeled on the screenshot).

<mark>Po2:<mark>

Top Switch: `int f0/12` and `int f0/10`

Middle Switch: `int f0/12` and `int f0/9`


<mark>Po1:<mark>

Middle Switch: `int f0/11` and `int f0/10`

Bottom Switch: `int f0/12` and `int f0/10`

## The Problem 
<img width="834" height="514" alt="bundling problem on int f0:12" src="https://github.com/user-attachments/assets/bd7137d7-858f-4539-bc3f-671b8aa71aa9" />

Po1 is bundled successfully but Po2's `int f0/12` is not bundled at all. 

- a input of `P` means that a bundle is successful
- a input of `s` means that the physical link is up but a misconfiguration prohibits an EtherChannel to form
- a input of `D` means that the physical link is down

## Checking the configurations on Middle and Top Switch

<img width="844" height="511" alt="po1 has trunk but not po2" src="https://github.com/user-attachments/assets/f92fcf70-b4a3-4990-bfcd-b89c62d20574" />

In order for an EtherChannel to bundle properly, you have to set each link that you plan to bundle together as trunk ports. You cannot have one of them as an access port because traffic from all VLANs must transverse through the bundled links. 

In this case, I purposely did not allowed any VLANs (despite having the `trunk` command configured) to travel through Po2. 

## Fixing the Problem by adding the `allowed vlan` command 

<img width="845" height="512" alt="int f0:9 allow trunk" src="https://github.com/user-attachments/assets/526b718c-d5f9-4bfb-8cab-70c082c3047c" />

<img width="853" height="508" alt="confirming command " src="https://github.com/user-attachments/assets/e9a5792b-45a2-41e9-bfe9-93a1d5711654" />

The command I typed showed up when I ran `show run`.

<img width="854" height="506" alt="it works!" src="https://github.com/user-attachments/assets/9faff766-ce4c-443a-b698-9abab0750791" />

The problem was fixed and both Port Channels are working!

## Checking if I can receive an IP address 
<img width="847" height="511" alt="portfast on int f0:13 on top switch" src="https://github.com/user-attachments/assets/3c33b561-4895-47bf-aa33-fe4291a2a351" />

I connected (from my MacBook) onto my TopSwitch's `int f0/13` and enabled PortFast in order to connect much faster. 

<img width="905" height="508" alt="wireshark" src="https://github.com/user-attachments/assets/c4b65343-1db2-441e-a7a2-5b7194a9295e" />

I opened WireShark in hopes of receiving an IP address, but all I saw was that my computer was just sending DHCP Discover messages for a good minute. 

## A simple fix but a brainfart moment at the same time 

I apologize for the bizarre title but as someone who wants to work in the Networking field, there are so many things going through your head as far as troubleshooting, but a good network technician always starts from the fundamentals and works his/her way up. 

<img width="600" height="600" alt="osi" src="https://github.com/user-attachments/assets/40b2e5fa-5ed3-47c8-8d70-24dc85c3d689" />

<img width="1591" height="664" alt="logical topology 2" src="https://github.com/user-attachments/assets/b04aaf22-0fca-4932-aa65-1834a459d9db" />

When I was going through the troubleshooting process, I skipped right ahead and started looking for any misconfigurations on my Layer 2 devices and also my router's DHCP pool (Layer 3 on the OSI model). I realized that I wasn't even connected to my router from my MiddleSwitch `int f0/24`. 

## Explanation and Receiving an IP Address 
<img width="903" height="509" alt="success wireshark" src="https://github.com/user-attachments/assets/deb9167d-9b49-4710-9bb1-cbfd350ce306" />

<img width="866" height="507" alt="Screenshot 2026-10-21 at 2 56 25 PM" src="https://github.com/user-attachments/assets/9a1f0b1b-5b91-4f82-8b1d-c1b34bcd7ca2" />

Troubleshooting in Networking will always have it's different process depending on the situation and problem that you are facing. Most importantly, you should always consider using the OSI model. You can save yourself minutes if not hours of troubleshooting if you are doing the process right. 

What should take you 5 minutes by connecting a simple cable, should not take you 40 minutes if you jumped the gun and skipped ahead to Layers 2-7. I learned that the hard way...



