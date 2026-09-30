# BPDU Guard & BPDU Filter — Interaction Troubleshooting

## Overview
While experimenting with Spanning Tree security features on my home lab 
(3x Cisco Catalyst 2960 switches + 1x Cisco 1921 ISR), I discovered an 
interaction between BPDU Guard and BPDU Filter when both are 
configured on the same interface. What started as routine PortFast/BPDU 
Guard testing turned into a deeper investigation of how these two features 
actually behave together.

## Environment
- **TopSwitch** and **MiddleSwitch**, connected via `int f0/1` on each
- PortFast disabled on Fa0/1 for this test (normally used on end-device 
  access ports to skip the 30-second STP listening/learning delay)
- Enabled both BPDU Guard & Filter on both `int f0/1` for both switches in order to see their behavior 

## The Problem
I expected that if MiddleSwitch's Fa0/1 had BPDU Guard enabled, the port 
would go `err-disabled` the moment it received a BPDU from TopSwitch. 
Instead, both interfaces stayed up with no errors — as if no BPDUs were 
ever exchanged at all.

## Let's Walk Through the Initial BPDU Guard & Filter Configurations First!


## Top Switch BPDU Guard Configuration
<img width="893" height="582" alt="Screenshot 2026-09-30 at 1 56 58 PM" src="https://github.com/user-attachments/assets/0178b8d7-5d03-4557-8744-7935d70b7cef" />


## MiddleSwitch BPDU Guard Configuration 
<img width="890" height="776" alt="Screenshot 2026-09-30 at 2 10 48 PM" src="https://github.com/user-attachments/assets/07109075-c070-465d-b0f6-1fe6019aa7ba" />

## Receiving the `err-disabled` message 
<img width="891" height="780" alt="Screenshot 2026-09-30 at 2 18 52 PM" src="https://github.com/user-attachments/assets/1efb564e-5c4e-4748-8cdf-347c28d25d11" />

- After configuring both guards on both `int f0/1`, I got an `err-disabled` message right after, confirming that the configuration worked!

## Investigating the `err-disabled` recovery modes & time 
<img width="895" height="517" alt="Screenshot 2026-09-30 at 2 29 38 PM" src="https://github.com/user-attachments/assets/053d7168-5bde-4918-9990-5be3ea5dc91b" />
<img width="893" height="648" alt="Screenshot 2026-09-30 at 2 29 09 PM" src="https://github.com/user-attachments/assets/e31e10c8-98c6-4c16-8af7-3cca2b47cb40" />
<img width="895" height="513" alt="Screenshot 2026-09-30 at 2 30 23 PM" src="https://github.com/user-attachments/assets/3e6766e7-5351-4280-b6b6-970dc0698780" />
<img width="897" height="586" alt="Screenshot 2026-09-30 at 2 30 55 PM" src="https://github.com/user-attachments/assets/d8588f62-0a32-4146-adfb-c0ef9a21ddce" />

- BPDU Guard recovery mode enabled but red boxes are circled incorrectly

<img width="894" height="573" alt="Screenshot 2026-09-30 at 2 33 16 PM" src="https://github.com/user-attachments/assets/5d910a7d-72ae-4a67-8f67-635511330c4b" />

- Middle Switch also received the `err-disabled` message!

## Note to audience!
The interface will not go in the `up` state unless the problem is solved. It will keep on reactivating the interface based upon
the interval seconds that you configured. Like my example, every 30 seconds the interface will try to re-activate but will turn back into the 
`err-disable` state once again. 

\* Interval seconds do not have to match between each switch \*



