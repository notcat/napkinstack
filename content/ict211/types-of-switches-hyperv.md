---
title: types of switches hyperv
date: 2022-09-27T20:42:49.000Z
draft: false
tags: [ict211, hyperv, virtualization, networking]
summary: the types of switches you can use in hyperv, and what they do
---

## switches

the types of switches you can use in hyperv, and what they do:

-   private switch
    -   the private switch is a completely isolated environment, where only the virtual machine guests with this switch will be able to talk to each other
-   external switch
    -   the external switch uses a nic that you select and forwards all traffic out the nic. 
-   internal switch
    -   the internal switch is much like the external switch, however the host machine is able to connect and talk to the virtual machines through an internal hyper-v adapter on the host operating system
-   default switch
    -   the default switch simply allows the machine to borrow and work through the nic of the host machine, acting as if there are two machines on one ethernet cable, through nat. the default switch is just the internal switch with nat

note: this is specific to hyperv, as different hypervisors could use different terminology or functionality.
