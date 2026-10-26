# Port Security - Why every device looked like the same MAC address 

## Overview
Port Security limits which/how many MAC addresses are allowed per each port on a Cisco switch. Without it, a laptop, a rogue switch, or 
an attacker can connect to an open port and connect to the network. I configured port security on one port on my TopSwitch, in which
I later found out a problem that generated one MAC address only. 

## Cisco Equipment
- 3x Cisco 2960 Switches
- 1x Cisco 1921 Router 


## Environment 
<img width="796" height="332" alt="logical topology" src="https://github.com/user-attachments/assets/03b702ae-200b-42c4-ba0f-8120068f5cbd" />

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

## Checking the MAC address of the USB-C adapter <mark>(THIS IS IMPORTANT)<mark>

<img width="896" height="587" alt="amazon adpater" src="https://github.com/user-attachments/assets/1ab03fcd-ba86-419d-b8ef-198e4d3b8093" />

<img width="896" height="587" alt="mac address of adapter" src="https://github.com/user-attachments/assets/3508f0bb-88ef-458a-b24d-731a509ba73b" />



## Configuring Port-Security on `int f0/2`
<img width="893" height="514" alt="using the mac address command " src="https://github.com/user-attachments/assets/80260d1e-9cc8-4704-99be-a563e80f6f4b" />

- I used this command in order to manually configure this MAC address to save in the MAC address table. I thought that my port was
  going to go in the `err-disabled` state once a different MAC address from another device passes through `int f0/2`. 

<img width="900" height="509" alt="maximum of 1" src="https://github.com/user-attachments/assets/809d59d7-d905-4cbd-b0db-9dc9932ed6e8"/>

- Configured a maximum of 1 Mac address allowed for this port, which should be the adapter's MAC address.

## Testing Port-Security 
<img width="899" height="510" alt="unplug cable to connect to mac" src="https://github.com/user-attachments/assets/c366104a-6f13-46ce-978e-35af03bcb576" />
<img width="896" height="512" alt="show mac address table " src="https://github.com/user-attachments/assets/dba5ff3f-ec57-4699-ad2b-c154457f75f6" />

- I plugged the Ethernet cord into the port and into my Mac, in which it successfully read the MAC address of the adapter.

<img width="894" height="513" alt="adapter to iphone connected" src="https://github.com/user-attachments/assets/5bd05898-7914-435f-9c18-8b63181db0c3" />

- I proceeded to connect the Ethernet cord onto my iPhone in which I generated the same MAC address once more. <mark>In my head,
  I believed that the adapter was just something that allowed a device to connect via an Ethernet cord while the device itself used
  it's own NIC card for it.<mark>


<img width="839" height="512" alt="clear mac address table " src="https://github.com/user-attachments/assets/dd87bf82-0a04-4df0-adc5-98451a04fb02" />

- I went ahead and cleared the MAC address table in order to test out my MacBook once more, thinking that the address might've
  been burned in somehow.


<img width="850" height="518" alt="same mac again" src="https://github.com/user-attachments/assets/f42cee97-d847-442c-b07c-28506252127a" />

- Generated the same MAC address once more.

## The Realization
If you go back and look at the MAC address that I posted on the "THIS IS IMPORTANT" section, you can see the USB-C to Ethernet MAC address, which is 6c:6e:07:16:6f:ee. 
- *(Cisco IOS displays MAC addresses in dotted format (xxxx.xxxx.xxxx), while macOS uses colons. These are the same address, so the adapter’s MAC matches the one secured on the switch port.)*

Each device nowadays have Wi-Fi chips that have their own MAC address. Back then, you can use an Ethernet cable to physically 
connect it to your own device (as you can see in the picture below.) 

<img width="200" height="200" alt="ethernet" src="https://github.com/user-attachments/assets/7fe4ada4-316f-41f7-a02d-1f88a65795f8" />

That means that your physical Ethernet port has its own Mac address, your Wi-Fi chip has it's own, and most importantly the 
<mark>USB-C to Ethernet adapter has it's own MAC address!<mark> 

No matter how many devices I use, I used the same adapter which means the same Mac address will be generated on the Cisco switch. 

## Triggering the `err-disable` feature on a NEW INTERFACE!
I decided do this lab on `int f0/13` instead of `int f0/2`, just to confirm there wasn't a misconfiguration. 

<img width="854" height="513" alt="sticky" src="https://github.com/user-attachments/assets/b2f34f58-d72f-4958-8986-a3cc26526795" />
<img width="848" height="516" alt="sticky 2" src="https://github.com/user-attachments/assets/4dc70fe8-1d08-4287-adc7-eed1286b59f6" />
<img width="849" height="513" alt="adding a new sticky" src="https://github.com/user-attachments/assets/5ef88391-4179-48ae-bdc5-3358a16476bb" />
<img width="859" height="514" alt="max of 1" src="https://github.com/user-attachments/assets/62c34d4e-93bb-428a-9520-fc5fe967265b" />



- I had to remove the sticky command and manually configure a new one (besides the adapter MAC) in order to trigger the `err-disable` feature. The port will not allow any other MAC besides the one I manually configured, in which I also had to put a maximum of 1 Mac-address for `int f0/13`.

<img width="860" height="516" alt="psecure" src="https://github.com/user-attachments/assets/911ccfe0-4119-4ff2-bc58-c185e9a689f2" />

- The psecure `err-disable` feature has been enabled.

<img width="845" height="513" alt="checking the mac address table " src="https://github.com/user-attachments/assets/6eb6ca3b-0097-4821-92a9-5dacd20c6eb7" />

- Checking that the MAC address table is empty.

## A successful `err-disable` trigger and explanation!

<img width="863" height="510" alt="a successful trigger" src="https://github.com/user-attachments/assets/df15c389-a0ea-4af7-bd77-74781552ba2e" />

A successful `err-disable` log message was displayed after the adapter's MAC address was read on the port! There will always be different MAC addresses on each and every NIC card that a device holds, in which I learned that an Ethernet adapter has one of its own.

- Generally speaking, a MAC addresses consists of 6 bytes or 48 bits.
- The first 3 bytes consists of the OUI, which a manufacturer assigns these globally to all of their devices (such as Apple, Dell, Cisco, etc).
- The last 3 bytes consists the unique specific device ID, in which no other device should have the same. Technically I could buy another adapter since the last 3 bytes would be different. But am I going to waste $25 just to do that? Nah

<img width="540" height="406" alt="mac" src="https://github.com/user-attachments/assets/9d606ed5-1bf2-48e9-9f6e-89b611731eb5" />

