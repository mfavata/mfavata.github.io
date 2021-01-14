---
layout: post
title: "RangeForce SOC 1 Analyst Overview/Review"
date: 2021-01-14
categories: Training
permalink: /RangeForce
---

Over the holidays I was lucky enough to be one of the winners of a BattlePath giveaway from [RangeForce](https://www.rangeforce.com). I have tweeted about them quite a bit and have recieved a lot of messages asking for more information on the platform and what I think about it. I want to do a quick run down of what is covered in the SOC 1 BattlePath and how I feel after having taken it. **TL;DR** - RangeForce is great platform and I recommend you at least register a community edition account and check it out for yourself.

First, a little about the platform. RangeForce descibe themselves as "a scalable cloud-based platform providing hands-on measurable simulation training for cybersecurity and IT operations professionals." They describe their BattlePaths as "hands-on training, job-related challenges, and a capstone exercise in our highly realistic, cloud-based cyber range. The skills learned are all real – featuring real IT infrastructure, using real security tools, and battling real cyberattacks." When you launch one of their modules, you'll be placed into a browser-based VM running either Linux or Windows, with all of the tools available that you need for that particular lesson/challenge.

Topics covered in the SOC 1 Analyst BattlePath include:

- Splunk basics, alerts and visualizations
- Wireshark basics
- E-mail url and header analysis
- Windows and Linux logging
- YARA overview and rule writing/management
- Regex
- Suricata basics, rules and rule management
- Windows Procmon
- Malware Analysis using Virustotal and tools like strings and file at the linux command line

The modules that really stood out to me were the ones involving Splunk and E-Mail analysis. Being able to get hands-on with a tool like Splunk and learn some of the inner workings and how to properly format search queries and develop alerts and visualizations seems like something an entry level SOC Analyst should know. You will utilize Splunk in the capstone challenge to identify various threats that you need to respond to. A great follow up to these modules would be doing something like [Boss Of The SOC v1 - SIEM Case Investigation](https://cyberdefenders.org/labs/15) available from Cyber Defenders.

The E-Mail analysis modules go in depth on using several online tools to extract and scan urls from suspicious e-mails along with tools used to analyze message headers. You will also learn to manually examine e-mail headers. After learning these things, you are given an e-mail challenge to "test your ability to identify phishing, spoofing, malicious attachments, and link analysis." Hands-on training like this is invaluable to someone just entering the field. 

To solve the challenge, you'll utilize manual header review along with several online tools, like the following:

- [urlscan.io](https://urlscan.io)
- [Google Admin Toolbox - Message Header](https://toolbox.googleapps.com/apps/messageheader/)
- [ipinfo.io](https://ipinfo.io/)
- [MX Toolbox](https://mxtoolbox.com/)

After completing 10 modules, you are left with the capstone challenge to complete. This challenge will have you responding to a suspected phishing e-mail, SSH Brute Forcing, a web attack and a port scan. You are given postfix, Suricata, fail2ban and a network diagram to complete the challenge. Postfix was not covered in any of the previous modules, but there is a hint available on how to configure it for what you need to do. There are no hints available for the rest of the challenge.

The Suricata modules taught you everything you need to know to write rules to deal with some of the attacks that are happening on the system. You can complete the challenge without using fail2ban, but it won't take you long to do a little Google searching to find out to configure and use it. I recommend you do, because it is not very complicated to set up but it is a very powerful tool.

After you complete the challenge, RangeForce will award you the SOC 1 Analyst Elite badge through Acclaim. 

[<img src="https://images.youracclaim.com/size/340x340/images/5b2c83f9-e183-4f31-8e34-57e88c537f66/Current_Badges__3_.png">](https://www.youracclaim.com/badges/1aeab567-ef3f-4928-bc9f-1684d508b495/public_url)

Overall I am very happy with the time I spent on the RangeForce platform. I feel very confident in using various tools now that I did not know how to use previously. I hope to see more hiring managers and organizations recognize the value of the hands-on training provided and I think it is a great addition to Net+/Sec+. I plan on getting their SOC 2 and Threat Hunter BattlePaths in the future when I am able to.

RangeForce has a [Community Edition](https://go.rangeforce.com/free-cyber-security-training-community-edition) available for free that will allow you to check out what their training is like, and I recommend you contact them and set up an account. There are currently just over 20 modules available for free that cover things like DevOps, Microsoft Security, Tools Used in the SOC and Web Application Security. So don't take my word for it, check it out for yourself!
