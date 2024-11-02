---
layout: page
title: X-Ray
category: Malware
type: ctf_huntressctf2024
desc: X-Ray
img-link: /writeups/ctf/huntressctf2024/img/x_ray/1.png
---


# [Malware] X-Ray
The following challenge description has been given:<br />
![lol it didn't load](img/x_ray/1.png)<br />
We checked the filetype of the given file but it didn't return notable.<br />
![lol it didn't load](img/x_ray/2.png)<br />
We have a hunch that it may be a quarantined version of the malware created by Windows Defender. We ran `dexray` with it.<br />
![lol it didn't load](img/x_ray/3.PNG)<br />
It worked! And it's a dotnet so it's easy to decompile with ilSpy.<br />
![lol it didn't load](img/x_ray/4.png)<br />
We looked at the Main function and we can see two hex blob loaded.<br />
![lol it didn't load](img/x_ray/5.PNG)<br />
We are also curious about the `otp()` function. Turns out that the two bytes are just being XORed together.<br />
![lol it didn't load](img/x_ray/6.PNG)<br />
We used Cyberchef to do the XOR operation and got the flag.<br />
![lol it didn't load](img/x_ray/7.PNG)<br />

