---
title: pwnagotchi ai not starting fix
date: 2022-09-26T21:44:03.000Z
draft: false
tags: [pwnagotchi, fix, ai]
summary: on the newest version of pwnagotchi (at this time, v1.5.5), the numpy library causes issues with starting the ai.
---

## problem

on the newest version of pwnagotchi (at this time, v1.5.5), the numpy library causes issues with starting the ai.

to fix this, we should install a specific version of numpy instead of the one that is installed by default.

`sudo pip3 install numpy==1.20.2`

reboot the pwnagotchi either using the web interface or command line:

`sudo reboot`
