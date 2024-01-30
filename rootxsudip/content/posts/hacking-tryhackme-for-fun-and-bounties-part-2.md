---
title: "Hacking TryHackMe for Fun and Bounties 👀 Part 2"
date: 2022-06-05T18:53:44+05:30
draft: false
toc: false
images:
tags:
  - IDOR
  - bug bounty
---

Hello guys, here is the 2nd part of blog. This is the most critical bug i have ever found in TryHackMe, using this bug an attacker can edit and delete any rooms of THM.  
I uploaded the POC video to Youtube.  
**Shortest Bug Explanation:** Any room is edited through a parameter with a room name, i created two room with two different accounts. While deleting/editing the 1st room name i intercepted the request and change the 1st room to 2nd room name, it throwed 403. In next attempt i added a second parameter(Parameter Pollution) with the 2nd room name, the check bypassed and the other room got delete/edited.  
**POC video:**  
[![Hacking THM Part 2](https://img.youtube.com/vi/eNfgWbU1N3M/hqdefault.jpg)](https://youtu.be/eNfgWbU1N3M)
