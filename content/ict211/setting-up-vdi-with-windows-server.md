---
title: "setting up vdi with windows server"
date: 2022-10-17T02:06:00Z
draft: false
tags: [ict211, hyperv, virtualization, networking, virtual desktop infrastructure, nested virtualization]
summary: figuring out how to setup virtual desktop infrastructure for the first time has been nothing but pain and agony for me. this guide attempts to help ease the pain for you with setting up vdi with windows server.
cover: /notes/images/vdi-diagram.png
---

## summary
i expect for most of you to already know how to setup everything except VDI, so i wont go into detail about anything other than the VDI. there is many more detailed and better guides out there for that stuff.

---

## basic topology
in this deployment, we will have 3 windows servers (i am using windows server standard 2022), and 1 switch (2 if you want WAN access through the VPN)

all of the servers are on the same private switch

![screenshot of topology](/notes/images/vdi-diagram.png)


| server name |    IPv4 address   |  roles                      | domain joined? |
|:-----------:|:-----------------:|:----------------------------|:--------------:|
|     VPN01   |   10.0.100.1/24   |  RRAS (VPN setup)           | no             |
|     DC01    |   10.0.100.2/24   |  AD DS, DHCP, NPS (RADIUS)  | yes            |
|     VDI01   |   10.0.100.3/24   |  VDI (RDCB, RDWA, RDVH)     | yes            |

---

# setting up vdi on VDI01

---

### prereqs
- make sure VDI01 can do nested virtualization
  - for proxmox, follow this article: ["Nested Virtualzation - Proxmox VE"](https://pve.proxmox.com/wiki/Nested_Virtualization)
  - for hyperv, follow this article: ["Run Hyper-V in a Virtual Machine with Nested Virtualization"](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)

- make sure it can talk to DC01, (ping or whatnot)

- make sure that the DHCP server on DC01 has a pool and is working correctly

- make sure the server is domain joined

- make sure you are logged in as a domain user ( NETBIOSHERE\Administrator for example )

- make sure you have enough storage for multiple vms (128gb - 256gb). it will not be able to create them without it.

- make sure you have enough ram on the VDI server to have multiple vms (8gb or 12gb of ram is probably good enough for a few vms)

---

### vdi role installation
1. click "add roles and features"
2. click "remote desktop services installation"
3. choose "standard deployment"
4. choose virtual machine-based desktop deployment
5. press next
6. add `VDI01.your.domain.com` to the selected computers by clicking the arrow button
![screenshot of add roles and features wizard](/notes/images/vdi-rdcb.png)
![screenshot of add roles and features wizard](/notes/images/vdi-rdcb2.png)
7. press next
8. do the same for the "rd web access server", add the `VDI01.your.domain.com` to the selected computers
9. press next
10. do the same for the "rd virtualization host server", add the `VDI01.your.domain.com` to the selected computers
11. press next
12. make sure you select "restart the destination server automatically if required", and then click "deploy"

### creating a reference image with sysprep
for vdi, we need to have a virtual machine that we use to act as the guest's computer when they connect to the VDI.

1. open up hyper-v manager
2. we need to create a new external switch, for the vm to talk to the rest of the network
3. click virtual switch manager
4. create a new switch with the type "external network", and use the same network adapter as the one that connects to the rest of your internal network with the domain controller on it.
![screenshot of newly created switch](/notes/images/vdi-hypervswitch.png)

---

we then need a new windows 10 (or 11) virtual machine to have a copy from
1. name it something memorizable like "Staff-Master"
2. make sure to select an enterprise version of windows (like education, or enterprise)
3. make sure to use that newly created switch
4. setup the windows install and get it into the desktop
![screenshot of desktop](/notes/images/vdi-windowsdesktop.png)
5. **MAKE ABSOLUTE SURE** that you get a DHCP ip address, and that you can talk to the domain controler inside the vm. if you can not, future steps will fail.
6. **MAKE SURE YOU CAN TALK TO THE DOMAIN CONTROLLER, AGAIN. BE DOUBLE SURE.**
7. make any needed changes, like installing software. for testing, i created a new folder on the C: drive and a new text file inside.
8. go to the C:\Windows\System32\Sysprep folder
9. **shift** right click the empty space and select "open powershell window here"
![screenshot of option](/notes/images/vdi-powershelloption.png)
10. in the window, enter `.\sysprep.exe /generalize /oobe /shutdown /mode:vm`
11. wait for it to finish and shutdown, DONT turn back on the virtual machine

we now have a sysprepped virtual machine for the VDI clients.

### configuring vdi
we need to have an OU in the active directory for the computers to live in, if you created the traditional "your.domain.com" OU at the top level, you can just use that.

for a real environment, we should organize them better and put them where they belong, but for this test environment, it doesnt matter.

1. click on "remote desktop services"
![screenshot of remote desktop services](/notes/images/vdi-remotedesktopservices.png)
2. click "tasks" next to the "deployment overview" box
![screenshot of tasks selected](/notes/images/vdi-tasks.png)
3. click "edit deployment properties"
4. click "active directory" on the left
5. select your domain
6. select the organizational unit, for me, i selected `your.domain.com`, as i already had it created. this is an OU inside my active directory.
7. it will say a permission error, you can fix it by clicking "apply"
![screenshot of permissions](/notes/images/vdi-permissions.png)

we now have the correct permissions to continue to create the virtual desktop collection.

---

1. select "create virtual desktop collections
![screenshot of virtual machine collection](/notes/images/vdi-virtcollection.png)
2. give it a name, this is the name that will be given when people connect to the pc. i have mine "Staff"
3. select "pooled virtual desktop collection"
4. select your master VDI image we created earlier, for me, its called "Staff-Master"
5. select "provide unattended installation settings" and press next
6. select your active directory, and then select the OU you added permissions for earlier. (i had to select "-" for some reason, not sure why)
![screenshot of unattended page](/notes/images/vdi-unattended.png)
7. specify the virtual desktops to be created, i selected 2. change the prefix of the machines. i used "Staff-" and a suffix of "0"
8. next
9. you can specify a new place for storage, but the defaults are fine. make sure you have enough storage on the VDI server for multiple vms to be created (probably 128gb or 256gb is good)
10. you can disable "enable user profile disks", as we dont really need them for our testing purposes. you can specify a path if you do want them however,
11. click create

check hyper-v to make sure that the vms are created, and you can also check the "networking" tab to see if it got an ip address from the dhcp server

---

### success?
you should now be able to access the rdweb portal. go to the ip address of the server, `10.0.100.3`

![screenshot of iis default page](/notes/images/vdi-iis.jpg)

1. go to the ip address with /rdweb appended to the end of the ip
`10.0.100.3/rdweb`
![screenshot of rdweb page](/notes/images/vdi-rdwebpage.png)
2. login with your domain credentials
![screenshot of rdweb page](/notes/images/vdi-stafffolder.png)
3. click on the "Staff" collection
![screenshot of connecting](/notes/images/vdi-connect.png)
4. click connect!
![screenshot of logging into the desktop](/notes/images/vdi-login.png)

you should now be connected!
![screenshot of sysprepped image](/notes/images/vdi-syspreppedimage.png)

we can see the file I created earlier when I sysprepped the "Staff-Master" machine

---

## you should now have a working VDI infrastructure! :) 
hope this helps!