---
title: "finding peoples location with just a wifi ssid"
date: 2022-10-01T12:13:06Z
draft: false
tags: [security, opsec, cybersec, wardriving]
summary: by using tools such as wigle.net, you can find (most) people's and businesses location by just a wifi ssid name
cover: /notes/images/wigle.png
---

[wiggle](https://wigle.net) is a online tool used for indexing and storing the crowdsourced information of accesspoints by name online. it indexes the location, name, security, (wpa2, wpa3, etc) and so much more. there is a "WiGLE WiFi Wardriving" app for android that allows you to submit networks in your local area, or wherevere you go with the app.

this website is mostly used for gathering information and reports about the current state of networking in the world, and auditing security for businesses. wigle can help you identify if there are rogue access points around businesses, acting as "an evil twin," which can be extremely important for businesses with high security.

## the juicy bits (finding the location of someones access point)

we can use wigle to find access points location reported by wigle by the wifi access point ssid. we need to create an account first to query the database, and filter by various numbers of parameters 

go to https://wigle.net/mapsearch and log in with your account

under "SSID / Network Name ...." put the wifi ssid of the one you would like to search or know more about (what type of security)

## go wild

have fun with this, dont do anything bad, and be good out there.

always be aware of attacks that someone might use, so you can better protect yourself against it. if your access point is named "SHAW-xxxxx", there is a good chance the attacker will go for you over an access point named "i love foss". an ssid name of "SHAW-xxxxx" gives you a lot of information about the potential victim, like what kind of router or isp they are using, and can be a good gague of whether or not the person has changed their password from the default.

the whole point of wardriving is to scope out potential targets and if you are blatantly broadcasting that you might be a potential target with an unsecured and unconfigured access point, it could make you a target.

## security

at the time of writing, just 0.03% of access points use wpa3. (according to wigle.net) this presents a big security risk as wpa2 is inheriently flawed and a wpa2 handshake can be bruteforced quite quickly, compared to wpa3. some router manufactorers and people decide to use numbers only for the password and this can be bruteforced in seconds with good hardware.

tldr: dont use your phone number as your password. too many networks are cracked by just a 10 number bruteforce, and switch to wpa3 if possible