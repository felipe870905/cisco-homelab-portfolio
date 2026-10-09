# Port Security - Why every device looked like the same MAC address 

## Overview
Port Security limits which/how many MAC addresses are allowed per each port on a Cisco switch. Without it, a laptop, a rogue switch, or 
an attacker can connect to an open port and connect to the network. I configured port security on one port on my TopSwitch, in which
I later found out a problem that generated one MAC address only. 


## Environment 
- TopSwitch, `int f0/2` and `int f0/13`
- Devices I tested: MacBook Pro, iPhone 16 Pro, and a Windows laptop *(this windows laptop has its own Ethernet port and an adapter was not used)*
- USB-C to Ethernet adapter (which was used to connect the MacBook and iPhone into a switch port.)


## The Problem
I was getting the same MAC address repeatedly when I switched from my Mac, to my iPhone, and Windows laptop continuously, expecting for a 
different MAC address to generate. I will later explain why this happened. 

## Let's walk through the processing of enabling Port-Security first!
First of all, port-security can only be enabled on ports that are configured as access ports only! You cannot configure port-security 
on a port that is configured as a trunk port, dynamic desirable, or dynamic auto. 
<img width="899" height="642" alt="dynamic desirable command" src="https://github.com/user-attachments/assets/4b9dfc7e-0249-4d78-b64d-a9977c7f1e36" />
<img width="894" height="705" alt="show dynamic desirable command" src="https://github.com/user-attachments/assets/86080a92-3bc1-450d-bb59-689ffb0d028a" />
<img width="898" height="643" alt="command not accepted" src="https://github.com/user-attachments/assets/10af05cf-458e-4126-96ec-a1ccd84875f5" />
- We can see now that this command was rejected since I configured it as dynamic desirable. 
<img width="891" height="642" alt="switchport mode access" src="https://github.com/user-attachments/assets/dcaeabde-479c-4e42-9eb3-14d51a8f5e6b" />
<img width="899" height="527" alt="command gets accepted" src="https://github.com/user-attachments/assets/af49b8f4-6c40-4580-bdb6-b728ee1a48f3" />

- You can see now that the command was accepted after I changed it to an access port. 

## Checking the MAC address of the USB-C adapter (THIS IS IMPORTANT)
<img width="896" height="587" alt="mac address of adapter" src="https://github.com/user-attachments/assets/3508f0bb-88ef-458a-b24d-731a509ba73b" />
<img width="893" height="514" alt="using the mac address command " src="https://github.com/user-attachments/assets/80260d1e-9cc8-4704-99be-a563e80f6f4b" />

- I used this command in order to manually configure this MAC address to save in the MAC address table. I thought that my port was
  going to go in the `err-disabled` state once a different MAC address from another device passes through `int f0/2`. 

