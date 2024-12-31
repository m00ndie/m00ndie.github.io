---
title: "THM Jr Penetration Tester Certificate: My Experience"
date: 2024-12-30 14:49:00 +0000
categories: [TryHackMe, Certificate]
tags: [TryHackMe, Certificate, Penetration Testing, Ethical Hacking]
---

![Jr Penetation Tester Curriculum](assets/images/post-30-12-24/curriculum.jpg)

The Jr Penetration Tester is a Learning Pathway on TryHackMe. It is basically the starting point for individuals who want to enter into the field of Penetration Testing or Ethical Hacking. To access all contents of the Learning Path, you have to be a Premium User. The Premium Tier at the time of writing this costs 14 USD per month and for me I think the pricing is really reasonable.

In this blog post, I share my experiences, what I liked and disliked. What I learnt and how it has been beneficial to me now. Let's get into it.

## Structure of The Learning Path

As at the time I'm writing this, the learning path has eight modules;

- Introduction to Cyber Security
- Introduction to Pentesting
- Introduction to Web Hacking
- Burp Suite
- Network Security
- Vulnerability Research
- Metasploit
- Privilege Escalation

I will briefly explain what each module covers and what you should know. I will not be covering the first two modules.

## Introduction to Web Hacking

I was really happy that this was included in the curriculum. In these modern times, web applications are almost everywhere. Learning how to attack these applications is crucial for Penetration Testers. This is an introductory module so it does not go into much detail. It covers the basics like content discovery, subdomain enumeration and the most common vulnerabilities in web applications.

Each module in the Learning Path has a lot of labs to give you practical knowledge in looking for and exploiting these vulnerabilities. This module was easy to grasp as it doesn't more advanced topics.

## Burp Suite

Burp Suite is an essential tool for web application hacking. It has a lot of features such as the Burp Proxy that enables the capture of requests and responses between a user and a target web server.

In this module, you are thoroughly taken through Burp Suite and all it's features. You are taught to use the Burp Repeater, Burp Intruder and other modules in Burp.

I already had some experience with Burp Suite so this module was one of the easiest for me. This module also provides lots of avenues for practical hands-on hacking using Burp.

## Network Security

This module started off with Passive Recon and Active recon as well as the tools that can be used for each. You are then introduced to nmap. The section on nmap comprehensively goes into nmap and what it can be used for. It teaches you about Live Host Discovery, Basic and Advanced Port Scans, and the Nmap Scripting Engine (NSE) which allows nmap to execute scripts written in the Lua Language.

The nmap section taught me that the tool isn't just limited to basic port scans but it can do a lot of other cools things. The section on nmap also talks about the TCP flags, what they are, the function of each and how combining the different flags in different ways can get you to perform different types of scans against a target.

This module also teaches about Protocols and Servers. This basically talks about some common networking protocols such as FTP, SMTP and others and gives the user a fair idea of what it is.

## Vulnerability Research

This module talks about vulnerabilities, what they are, how to manage them and how to exploit them. It is a fairly easy and straightforward module so there isn't much I can say about it.

## Metasploit

Metasploit is a powerful tool that can support all stages of a penetration testing engagement so understanding how to use is is very important for penetration testers.

Just like with the Burp Suite module, this module is dedicate solely to Metasploit. It teaches users about the capabilities of Metasploit and how to use the tool properly. It gives the user an understanding of what each component does and how to use all these components together.

I learnt about generating your own payloads with msfvenom, the capabilities of meterpreter and a lot more. In playing CTFs especially, this knowledge has been very useful to me.

## Privilege Escalation

Finally, we have the Privilege Escalation module. This is in my opinion the most important module in the Learning Path. I have consumed a lot of content on Ethical Hacking but none ever talked about stabilizing a shell, techniques for privilege escalation on both Windows and on Linux.

In the first ever CTF I played, I had to stabilize the netcat shell I was using and also escalate my privileges from one user to another. The knowledge from this module really came in clutch and I am certain I am going to use this knowledge more and more in the CTFs I play and even in Penetration Tests.

This module was packed with a lot of vital information so I had to take quite a few breaks going through this so I didn't overload my brain. Nevertheless, I really liked this module.

## My Thoughts

The learning path is really well structured and the information in it was carefully explained. The labs that came with each module was really good for practical hands-on experience. For aspiring penetration testers and ethical hackers like myself, I would recommend they take this learning path as it sets a strong foundation and gives you the basic knowledge and skills needed to thrive.

![Certificate](assets/images/post-30-12-24/jr-penetration-tester-cert.jpg)

Thanks for reading! I'll see you in my next posts :-)
