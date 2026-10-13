# BPDU Guard & BPDU Filter — Interaction Troubleshooting

## Overview
While experimenting with Spanning Tree security features on my home lab 
(3x Cisco Catalyst 2960 switches + 1x Cisco 1921 ISR), I discovered an 
interaction between BPDU Guard and BPDU Filter when both are 
configured on the same interface. What started as routine PortFast/BPDU 
Guard testing turned into a deeper investigation of how these two features 
actually behave together.

## Environment
<img width="796" height="332" alt="logical topology" src="https://github.com/user-attachments/assets/4b3990c0-1207-4d2b-b050-fc92963f28df" />


- <mark>**TopSwitch** and **MiddleSwitch**, connected via `int f0/1` on each switch<mark>
- PortFast disabled on Fa0/1 for this test (normally used on end-device 
  access ports to skip the 30-second STP listening/learning delay)
- Enabled BPDU Guard first and the Filter on each switch
- Enabled both BPDU Guard & Filter on both `int f0/1` for both switches in order to see their behavior 

## The Problem
I expected that if MiddleSwitch's Fa0/1 had BPDU Guard enabled, the port 
would go `err-disable` the moment it received a BPDU from TopSwitch. 
Instead, both interfaces stayed up with no errors — as if no BPDUs were 
ever exchanged at all.

## Let's Walk Through the Initial BPDU Guard & Filter Configurations First!


## Top Switch BPDU Guard Configuration
<img width="893" height="582" alt="Screenshot 2026-09-30 at 1 56 58 PM" src="https://github.com/user-attachments/assets/0178b8d7-5d03-4557-8744-7935d70b7cef" />


## MiddleSwitch BPDU Guard Configuration 
<img width="890" height="776" alt="Screenshot 2026-09-30 at 2 10 48 PM" src="https://github.com/user-attachments/assets/07109075-c070-465d-b0f6-1fe6019aa7ba" />

## Receiving the `err-disable` message on the TopSwitch
<img width="891" height="780" alt="Screenshot 2026-09-30 at 2 18 52 PM" src="https://github.com/user-attachments/assets/1efb564e-5c4e-4748-8cdf-347c28d25d11" />

- After configuring both guards on both `int f0/1`, I got an `err-disable` message right after, confirming that the configuration worked!

## Investigating the `err-disable` recovery modes & time 
<img width="895" height="517" alt="Screenshot 2026-09-30 at 2 29 38 PM" src="https://github.com/user-attachments/assets/053d7168-5bde-4918-9990-5be3ea5dc91b" />
<img width="893" height="648" alt="Screenshot 2026-09-30 at 2 29 09 PM" src="https://github.com/user-attachments/assets/e31e10c8-98c6-4c16-8af7-3cca2b47cb40" />
<img width="895" height="513" alt="Screenshot 2026-09-30 at 2 30 23 PM" src="https://github.com/user-attachments/assets/3e6766e7-5351-4280-b6b6-970dc0698780" />
<img width="897" height="586" alt="Screenshot 2026-09-30 at 2 30 55 PM" src="https://github.com/user-attachments/assets/d8588f62-0a32-4146-adfb-c0ef9a21ddce" />

- BPDU Guard recovery mode enabled but red boxes are circled incorrectly


## Receiving the `err-disable` message on the MiddleSwitch
<img width="894" height="573" alt="Screenshot 2026-09-30 at 2 33 16 PM" src="https://github.com/user-attachments/assets/5d910a7d-72ae-4a67-8f67-635511330c4b" />

- Since both interfaces are sending BPDUs (since BPDU filter is not enabled yet), receiving the `err-disable` message on both switches means that the configuration was done correctly!


## Note to audience!
The interface will not go in the `up` state unless the problem is solved. It will keep on reactivating the interface based upon
the interval seconds that you configured. Like my example, every 30 seconds the interface will try to re-activate but will turn back into the 
`err-disable` state once again. 

\* Interval seconds do not have to match between each switch \*


## Enabling BPDU Filter on the MiddleSwitch
In the next screenshots, I enable the BPDU Filter on the same interface f0/1 on the MiddleSwitch, 
in which it will not send any more BPDUs. 
<img width="892" height="777" alt="middle switch bpdu filter " src="https://github.com/user-attachments/assets/1f6e843c-5f7d-4b38-aa3b-f243b4b056f0" />


## Encountering a BPDU Guard/BPDU Filter Problem on the same `int f0/1`
I decided to configure both Guard & Filter on the same interface to see the outcome (in which I will explain later). I first configured the filter on the MiddleSwitch and ran the `show run int f0/1` command.
<img width="892" height="772" alt="show run int f0:1 on the middle switch" src="https://github.com/user-attachments/assets/cec1d994-3d59-4a4a-a07c-9486d0be0c27" />

- I realized that no `err-disable` message had appeared on my MiddleSwitch, in which it should have been triggered since the TopSwitch interface is sending BPDUs.

<img width="889" height="715" alt="middleswitch int f0:1 went up " src="https://github.com/user-attachments/assets/235ba750-6bcc-4485-972a-a4d50e8167a3" />

- <mark>MiddleSwitch `int f0/1` went up which meant that the TopSwitch `int f0/1` also went up.<mark>
- <mark>\*(THIS IS IMPORTANT FOR THE EXPLANATION)\*<mark>

## What the BPDU Filter actually does when paired up with the BPDU Guard 
<img width="891" height="712" alt="bpdu guard disabled on middleswitch" src="https://github.com/user-attachments/assets/4480bf03-9854-4613-b811-3dc93378e60c" />

- I explicitly disabled the BPDU Guard command on the MiddleSwitch, but I realized that the filter command was placed first. 

<img width="892" height="713" alt="top switch show run int f0:1 bpdu guard command " src="https://github.com/user-attachments/assets/a647db3f-d0bd-4eaa-8dfc-be3b7274d20f" />

- The BPDU Guard is still configured on the TopSwitch and no Filter has been applied, meaning that the TopSwitch is sending
BPDUs to the MiddleSwitch.

<img width="892" height="778" alt="disabling the filter on the middle switch" src="https://github.com/user-attachments/assets/55e11f98-f791-49e7-a85e-4aadd4007550" />

- I went ahead and disabled the filter on the MiddleSwitch. 

<img width="893" height="778" alt="err-diable message again on the top switch" src="https://github.com/user-attachments/assets/b4a77b75-6267-4b37-a54b-6d173f589391" />

- The `err-disable` message appeared once more on the TopSwitch. 


## Enabling the Filter on the MiddleSwitch one last time 
<img width="893" height="780" alt="ENABLING FILTER ON MIDDLESWITCH ONCE MORE " src="https://github.com/user-attachments/assets/ab3724e4-e31a-4670-bda1-1301b4101c6c" />

<img width="888" height="642" alt="both interfaces go up (FINAL)" src="https://github.com/user-attachments/assets/d6ce07b1-5898-4459-adf2-a7b1d0c011a1" />



## EXPLANATION
- The message states that both interfaces went up after configuring the filter on the MiddleSwitch. Why is that?
- <mark>Well I realized that the BPDU Filter will always take priority first over the BPDU Guard. Since the TopSwitch is sending BPDUs to the MiddleSwitch and the MiddleSwitch only has the BPDU Filter command, it will automatically drop those BPDUs.<mark>
- <mark>The MiddleSwitch will not bother to listen to any BPDUs since it is not sending any, making both interfaces to go up like normal.<mark>
- When you look at the "I went ahead and disabled the filter on the MiddleSwitch" picture, the interface went on `err-disable`!
