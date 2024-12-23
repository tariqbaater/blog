+++
title = "Bandit"
date = "2024-12-23T05:49:12+03:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Tariq Baater"
authorTwitter = "tariqbaater" #do not include @
cover = "/over-the-wire.png"
tags = ["cybersecurity", "ctf", 'overthewire']
keywords = ["", ""]
description = "Overthewire CTF Writeup"
showFullContent = false
readingTime = true
hideComments = false
+++

# Bandit

### bandit0  
username: bandit0    
password: bandit0  

This one is fairly easy since the password is in the readme file

### bandit1  
username: bandit1  
password: **ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If**

to read files with characters as name we append ./ to the filename.

```bash
cat ./-
# output **263JGJPfgU6LtdEvgfWU1XP5yac29mFx**
```

### bandit2  
username: bandit2  
password: **263JGJPfgU6LtdEvgfWU1XP5yac29mFx**

to read files with spaces we can qoute the file.

```bash
cat 'spaces in this filename'
# output **MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx**
```

### bandit3  
username: bandit3  
password: **MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx**

using ls with -la flags to see hidden files and directories

```bash
ls -la inhere/
```

### bandit4  
username: bandit4  
password: **2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ**

This time in the 'inhere' directory has many files but we don't know which has the password, the easiest solution is to use grep and find for patterns. Since we arleady know the passwords in bandit contain small letters, capital letters and numbers. We will use the below regex pattern.

```bash
cd inhere
grep '[a-zA-Z0-9]' .
#output
grep: -file08: binary file matches
grep: -file02: binary file matches
grep: -file09: binary file matches
grep: -file01: binary file matches
grep: -file00: binary file matches
grep: -file05: binary file matches
-file07:**4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw**
-file03:d
grep: -file03: binary file matches
grep: -file06: binary file matches
grep: -file04: binary file matches
```

### bandit5  
username: bandit5  
password: **4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw**

In this challenge the password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

human-readable
1033 bytes in size
not executable

Using find is much easier if you know the details.

```bash
find . -type f -size 1033c 2>/dev/null | xargs cat
#output **HWasnPhtq9AVKe0dmk45nxy20cvUa6EG**
```

### bandit 6
username: bandit6  
password: **HWasnPhtq9AVKe0dmk45nxy20cvUa6EG**

The password for the next level is stored somewhere on the server and has all of the following properties:

owned by user bandit7  
owned by group bandit6  
33 bytes in size

Find has many flags making it easier to pinpoint to a file.

```bash
find / -user bandit7             -group bandit6 -size 33c 2>/dev/null | xargs cat
#output **morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj**
```

### bandit7  
username: bandit7  
password: **morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj**

The password for the next level is stored in the file data.txt next to the word millionth.

grep the file for the word millionth.

```bash
grep millionth data.txt
#output: millionth	**dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc**
```

### bandit8  
username: bandit8  
password: **dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc**



The password for the next level is stored in the file data.txt and is the only line of text that occurs only once.

In this challenge i used a combination of sort, uniq and awk to print exactly what is need.

```bash
cat data.txt |sort |  uniq -c | awk '{if ($1 < 10) print $0}'
#output: 1 **4CKMh1JI91bUIZZPXDqGanal4xvAg0JM**
```

### bandit9  
username: bandit9  
password: **4CKMh1JI91bUIZZPXDqGanal4xvAg0JM**

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.


```bash
strings data.txt | awk '{print $2}' | grep -E '.{32,}'
# output : **FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey**
```

### bandit10  
username: bandit10  
password: **FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey**

The password for the next level is stored in the file data.txt, which contains base64 encoded data

```bash
cat data.txt | base64 -d
#output The password is **dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr**
```

### bandit11  
username: bandit11  
password: **dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr**

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions, since i enjoy using the terminal i founbd this easy way to decode rot13             using 'tr'.

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
#output The password is **7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4**
```

### bandit12  
username: bandit12  
password: **7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4**

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!).

This is where the challenges take a toll, we have to figure out how to decompress until we get the password.

If you cat the file, it is a hexadump with data2.bin which indicates that there is some compressed data within. Let's convert the file to binary.

```bash
xxd -r data.txt > binary
```

If you check the file it is a gzip file, so let's change extension of the file and unzip using gunzip.

```bash
mv binary binary.gz
gunzip binary.gz

ls
#output binary
```

If you check the file ype you'd see it's bzip2, so let's unzip using bunzip2.

```bash
bunzip2 binary

ls
#output binary.out
```

So you get the idea we have to unzip and decompress until we get an ASCII text file that contains the password for the next level.

We find the password after several decompressions as follows: The password is **FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn**


### bandit13  
username: bandit13  
password: **FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn**

The password for the next level is stored in /etc/bandit_pass/bandit14             and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on.

So we use tack -i to to ssh with the given key into the next challenge.

```bash
ssh -i sshkey.private bandit13@bandit.labs.overthewire.org -p 2220
cat /etc/bandit_pass/bandit14  
#output: **MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS**
```

### bandit14  
username: bandit14  
password: **MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS**


The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

So we use netcat to connect to localhost on port 30000, where we can access whatever service is opened to that port and post our key to access the next level password.

```bash
nc localhost 30000
**MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS** # current level password
#output: Correct!
# **8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo**
```

### bandit15  
username: bandit15  
password: **8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo**

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

I searched online on how to connect to server from client side using openssl and found the below code.

```bash
openssl s_client -connect localhost:30001
```

The above will connect to the server but with securely, then we can provide the required key to get the next level password.


### bandit16  
username: bandit16  
password: kSkvUpMQ7lBYyCM4GBPvCvT1            BfWRy0Dx


The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

To see ports that are listening we'll use 'ss'.

```bash
ss -tulnp | grep LISTEN
```

![image](/ss.png)

We see above that port 31790 stands out since it's between 31000 and 32000 and also it's size is 4096, let's try and send a secure handshake. I tried using openssl but i wasn't succesful so i resorted to netcat but with --ssl flag.

```bash
ncat --ssl localhost 31790
```

![image](/ncat.png)

When we connect to the server we provide current level's password and we get an ssh key for the next level as shown above.

### bandit17  
username: bandit17  
password: **EReVavePLFHtFlFsjn3hyzMlvSuSAcRD**

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

```bash
diff -u passwords.old passwords.new
```

![image](/diff.png)

The password is : **x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO**, as shown above.

### bandit18  
username: bandit18  
password: **x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO**

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

Since we know there is a readme file in bandit18             home folder and we have credentials, we can copy the file remotely using ssh from our attacking machine.

```bash
scp -P 2220 bandit18@bandit.labs.overthewire.org:/home/bandit18/readme .
```
![image](/scp.png)

And voila, we get the password for the next level.

### bandit19  
username: bandit19  
password: **cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8**

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

```bash 
./bandit20-do cat /etc/bandit_pass/bandit20
#output: **0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO**
```
![image](/setuid.png)


### bandit20
username: bandit20 
password: **0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO**

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

Using ’netcat’, we can create a connection in server mode - which listens for inbound connection. To have netcat send the password, I use echo and pipe it into netcat. The -n flag is to prevent newline characters in the input. Lastly, we let the process run in the background with &.

```bash 
echo -n 'GbKksEFF4yrVs6il55v6gwY5aVje5f0j' | nc -l -p 1234 &
```
![image](/netcat.png)


### bandit21
username: bandit21
password: **EeoULMCra2q0dSkYj561DX7s1CpBuOBt**

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

There is a bash script executing after every minute and it seems executable to all users, let' read the contents.

![image](/cron.png)


### bandit22
username: bandit22
password: **tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q**


A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

There is a bash script executing after every minute, let's read the contents. 

```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

It seems to check who is the current user and then hashes the user as a variable named 'mytarget', then creates a new file in tmp with the hash as it's name and dumps the next level password in it. All we have to do is figure out and the hashed file name and i can access the contents of the file.

```bash
echo 'I am user bandit23' | md5sum | cut -d ' ' -f 1
```

![image](/md5sum.png)

All we had to do was run part of the script and we will get the filename that the cronjob dumps the password into.


### bandit23
username: bandit23
password: **0Zf11ioIjMVN551jX3CmStKLYqjk54Ga**

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

```bash
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

If you cat the cronjob that is supposed to run as bandit24 you will see that it is saving bandit24 as a variable then changes directory to '/var/spool/$myname(which is the bandit24)/foo',  then there is a for loop that negates any file or folder starting with '.' or '..'; it checks if the owner is us then executes the remaining file for 1 minute, kills the process and removes the file.

So let's make a script that will be the remaining executed file in foo folder, __make sure you give proper permissions to make sure another user can write into the files__.


```bash 
#!/bin/bash 

cat /etc/bandit_pass/bandit24 > /tmp/{use mktemp -d}/pass.txt
```

```bash
touch script.sh && chmod 777 script.sh
touch pass.txt && chmod 777 pass.txt
chmod 777 /tmp/(use mktemp -d) 
cp script.sh /var/spool/bandit24/foo/script.sh
```

After 1 minute if you cat tha pass.txt file you will see the password.

![image](/script.png)


### bandit24
username: bandit24
password: **gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8**


A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
You do not need to create new connections each time.


With this one i used crunch to generate all possible 4 digit numbers and then used netcat to connect to the port and post the password and the pincode to access the next level password.

```bash 
crunch 4 4 0000 9999 -o pincode.txt

nc localhost 30002 < pincode.txt
```

Crunch is not installed in the bandit, so i resorted to bash script instead.

```bash
#!/bin/bash

# Generate all possible 4-digit numbers and pass them to nc
nc localhost 30002 < <(for i in $(seq -w 0 9999); do echo gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i; done)
```

if you run the sciprt eventually it will run a correct pincode and spit out the password.

![image](/brute.png)


### bandit25
username: bandit25
password: **iCi86ttT4KSNe1armKiwbQNmB3YJP3q4**

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.

Since we are on bandit25 we can cat /etc/passwd and see the shell of bandit26.

```bash
cat /etc/passwd | grep bandit26
# output: bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

if we cat /usr/bin/showtext we will see the below script which uses more and since the file is small it will probably run all commands in the script, so we have to improvize and reduce our terminal size to make the script execute only some of the commands.
I just increased my terminal fonts to imitate as if the terminal is small in size.

Now when we ssh into bandit26 more will executed and we can enter into vim mode using __v__, from there we can open files with :e or even run :shell. But since the default shell is being called through /usr/bin/showtext we can set the shell to bash; :set shell=/bin/bash and now we can spawn a proper shell with :shell.

![image](/more.png)


### bandit26
username: bandit26
password: **s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ**


This level is just a gift, all we had to do was use the binary in bandit26 home folder which executes any command as bandit27.

```bash
./bandit27-do cat /etc/bandit_pass/bandit27
```

### bandit 27
username: bandit27
password: **upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB**

There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo via the port 2220. The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.

```bash
git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo.git
```

If you cd into the repo you'll find a README which contains the password for the next level.

![image](/git.png)


### bandit28
username: bandit28
password: **Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN**

There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo via the port 2220. The password for the user bandit28-git is the same as for the user bandit28.

Clone the repository and find the password for the next level.

In this challenge we will clone the repo just like the previous level but there is no password in the README this time. If you cat README you it shows that the developer has some notes and this indicates there could be multpile git pushes, let's check the git log for more details.

[![image](/git-notes.png)

```bash
git log
```

![image](/git-log.png)

As you can see above we found a commit message that the developer leaked the password mistakenly.


### bandit29
username: bandit29
password: **4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7**

There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo via the port 2220. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.

Same as previous challenges but this time the developer is not using any passwords in production, this indicates he may be having another branch for features and testing. Let's check available branches.

```bash
git branch -r # the flag -r is to show remote branches
```

It seems there are remotes branches and dev seems to be the one we are looking for. If we switch to dev and cat README file we find the password.

![image](/remote-branch.png)


### bandit30
username: bandit30
password: **qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL**

There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo via the port 2220. The password for the user bandit30-git is the same as for the user bandit30.

Clone the repository and find the password for the next level.

```bash 
git clone ssh://bandit30-git@localhost:2220/home/bandit30-git/repo.git 
``` 

This gave me trouble a little bit but after researching i found out that there is a git command called 'tag' which can be used to reference a specific point in a repo.

```bash
git tag
```

When we run the command above we get a tag referenced as secret, we  can view with git show.

![image](/git-tag.png)


### bandit31
username: bandit31
password: **fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy**

There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo via the port 2220. The password for the user bandit31-git is the same as for the user bandit31.

Clone the repository and find the password for the next level.

When we cat the README it's telling us to push a file with below details.

![image](/git-push2.png)

All we have to do is create a file named key.txt with "May I Come in?" as it's content and push it to master.

```bash
git add key.txt
git echo 'May I Come in?' > key.txt
git commit -m "added key.txt"
git push origin master
```

After pushing the file we get the password as below.

![image](/git-push.png)


### bandit32
username: bandit32
password: **3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K**

This challenge has a service that takes our input as shell commands, the problem is it converts our commands into uppercase. We need to escape the service shell into a normal shell. the variable $0 has a reference to a shell.

This challenge has a service that takes our input as shell commands, the problem is it converts our commands into uppercase. We need to escape the service shell into a normal shell, the variable $0 in linux has a reference to a shell.

```bash 
echo $0
# output : bash or -zsh
```

When we just run $0 it'll break out the service and run the local shell, from there we can just cat the next level's password.

![image](/sub-shell.png)


### bandit33
username: bandit33
password: **tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0**

