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

<img width="845" height="512" alt="int f0:9 allow trunk" src="https://github.com/user-attachments/assets/526b718c-d5f9-4bfb-8cab-70c082c3047c" />

<img width="853" height="508" alt="confirming command " src="https://github.com/user-attachments/assets/e9a5792b-45a2-41e9-bfe9-93a1d5711654" />

The command I typed showed up when I ran `show run`.


