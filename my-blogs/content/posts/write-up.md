+++
title = "Mr.Robot THM Write Up"
date = "2024-11-30T20:10:23+03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Tariq Baater"
authorTwitter = "tariqbaater" #do not include @
cover = ""
tags = ["thm", "cybersecurity"]
keywords = ["", ""]
description = "Try Hack Me Write Up For Mr.Robot"
showFullContent = false
readingTime = true
hideComments = false
+++

# Try Hack Me Write Up For Mr.Robot

## First we start by initializing the machine with nmap

```bash
sudo nmap -p- -Pn <ip> | tee nmap.txt
```

We find that the machine is running on port 80 and 443, so we try to scan the ports with nmap scripts to find more information.

```bash
sudo nmap -p80,443 -sC -sV <ip> | tee nmap.txt
```


