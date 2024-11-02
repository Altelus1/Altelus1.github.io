---
layout: page
title: Ping Me
category: Malware
type: ctf_huntressctf2024
desc: Ping Me
img-link: /writeups/ctf/huntressctf2024/img/ping_me/1.png
---


# [Malware] Ping Me
The following challenge description has been given:<br />
![lol it didn't load](img/ping_me/1.png)<br />
We are greeted with a lot of obfuscated VBS code upon opening up the file.<br />
![lol it didn't load](img/ping_me/2.png)<br />
We copied the code after the "Execute" and pasted it on the Console.WriteLine on Tutorialspoint. We have the following output.<br />
![lol it didn't load](img/ping_me/3.png)<br />
It seems that there's nothing more with the deobfuscated code but pinging only the IP addresses obtained. However, if we are to treat each octet as an actual number and convert it to ASCII, it creates a readable string. It's actually the flag:<br />
![lol it didn't load](img/ping_me/4.png)<br />
