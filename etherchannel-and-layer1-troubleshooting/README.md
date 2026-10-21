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
Po1 is bundled successfully but Po2's `int f0/12` is not bundled at all. 

