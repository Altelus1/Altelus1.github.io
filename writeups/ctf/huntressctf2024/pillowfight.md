---
layout: page
title: Pillow Fight
category: Web
type: ctf_huntressctf2024
desc: Pillow Fight
img-link: /writeups/ctf/huntressctf2024/img/pillow_fight/1.png
---


# [Web] Pillow Fight
The following challenge description has been given:<br />
![lol it didn't load](img/pillow_fight/1.png)<br />
Below is the landing page:<br />
![lol it didn't load](img/pillow_fight/2.png)<br />
We can also see access the Swagger UI for the available APIs.<br />
![lol it didn't load](img/pillow_fight/3.png)<br />
Since it accepts "eval\_command", we imagine that it's going to be inserted in an actual `eval()` python command. So we treat the `img1` as an actual variable. We can then substitute this into an actual payload where we can use `__import__('os')...` syntax. As can be seen below, we have tried executing it in place of `img1`.<br /> 
![lol it didn't load](img/pillow_fight/4.png)<br />
We received a shell and it's already root! We also got the flag afterwards.<br />
![lol it didn't load](img/pillow_fight/5.png)<br />
![lol it didn't load](img/pillow_fight/6.png)<br />

