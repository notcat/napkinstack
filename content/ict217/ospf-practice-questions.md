---
title: ospf practice questions
date: 2022-09-27T21:02:52.000Z
draft: false
tags: [ict217, ospf, cisco, quiz, practice, questions]
summary: some practice questions revolving around ospf
---

## some practice questions revolving around ospf

### q1: router-id

* * *

![q1: router-id](/ict217/images/ict217-ospf-q1.png)

* * *

what does router router1 use as its ospf router-id?

the answer is: {{< inlinespoiler >}}the router-id is 172.16.15.10, the loopbacks address.{{< /inlinespoiler >}}

the reason is: {{< inlinespoiler >}}the router-id gets set to the loopback address if it is manually set. if there is no loopback address created, it will take the highest ip address and use that instead.{{< /inlinespoiler >}}