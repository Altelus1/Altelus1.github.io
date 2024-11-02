---
layout: page
title: Knight's Quest
category: Reversing
type: ctf_huntressctf2024
desc: Knight's Quest
img-link: /writeups/ctf/huntressctf2024/img/knightsquest/1.png
---


# [Reversing] Knight's Quest
The following challenge description has been given:<br />
![lol it didn't load](img/knightsquest/1.png)<br />
Once the challenge is started, we can access the website. Here is the landing page:<br />
![lol it didn't load](img/knightsquest/1.5.png)<br />
It instructs us to play the game and win to get the password to get the flag. We can download the game in the "downloads page" to play the game.<br />
We downloaded the file.<br />
![lol it didn't load](img/knightsquest/2.png)<br />
We run the binary and we tried to play the game.<br />
![lol it didn't load](img/knightsquest/3.png)<br />
The first boss is a spider:<br />
![lol it didn't load](img/knightsquest/4.png)<br />
The second boss is a ogre:<br />
![lol it didn't load](img/knightsquest/5.png)<br />
The last boss is Gorthmog, Destroyer of worlds with ridiculously health and attack points:<br />
![lol it didn't load](img/knightsquest/6.png)<br />
Of course we lost that one. One of the ways we can defeat Gorthmog is to have a very high attack power as well or if we can just reduce the health points to 1. We proceeded with the latter. The plan is if the health points is already hardcoded within the game binary, perhaps we can patch it to only set it to 1. So we get the hex value of the Gorthmog's health points.<br />
![lol it didn't load](img/knightsquest/7.png)<br />
It's 0x3b9ac9ff. We opened the binary to hexed.it and find the byte sequence `ff c9 9a 3b` and and set it to `01 00 00 00`. We actually found it<br />
![lol it didn't load](img/knightsquest/8.png)<br />
Then we patched it.<br />
![lol it didn't load](img/knightsquest/9.png)<br />
We saved the patched game and replayed it. After defeating the first two bosses, we can see the Gorthmog's health points is just 1 now. This is sure win since we always get to attack first.<br />
![lol it didn't load](img/knightsquest/10.png)<br />
We got the password.<br />
![lol it didn't load](img/knightsquest/11.png)<br />
We submitted it to the challenge URL and got the flag.<br />
![lol it didn't load](img/knightsquest/12.png)<br />
