+++
title = "Mr Robot"
date = "2024-11-30T21:10:55+03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "tariqbaater"
authorTwitter = "tariqbaater" #do not include @
cover = "/mr-robot.png"
tags = ["thm", "cybersecurity"]
keywords = ["", ""]
description = "THM CTF Writeup"
showFullContent = false
readingTime = true
hideComments = false
+++

This is the writeup for the Mr Robot CTF challenge.

## Solution
<!-- ![local_image](/mr-robot.png "Mr Robot") -->
First we start by enumarating the ports

```bash
nmap -p- -Pn -T4 <IP> | tee ports.txt
```

Then we run the nmap script to find more information on the ports discovered.

```bash
nmap -sC -sV -p <PORT> -T4 <IP> | tee ports.txt
```

It is good practice to run gobuster scan to find directories while busy enumarting the box further.

```bash
gobuster dir -u <URL> -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
```

After the gobuster scan we get the following directories;

- /robots.txt
- /key-1-of-3.txt
- /wp-login.php

There is some interesting content in the /robots.txt file.
