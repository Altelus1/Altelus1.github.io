---
layout: page
title: Lucky 1000
category: BinForCry
type: ctf_rootcon18finals
desc: Lucky 1000
img-link: /writeups/ctf/rootcon18finals/images/bin4cry/lucky1000/img1.png
---


# [BinForCry] Lucky 1000
We're given with this file.<br />
![lol it didn't load](images/bin4cry/lucky1000/img1.png)<br />
Output states that it's an ELF file.<br />
We loaded it on Ghidra and checked out main function. There seems to be an interesting function called `init`<br />
![lol it didn't load](images/bin4cry/lucky1000/2.png)<br >
Checking it out, there is the sequence of bytes that's being XORed to 0x13 and then printing it as the flag.<br />
![lol it didn't load](images/bin4cry/lucky1000/3.png)<br />
We extracted the values and XORed them to 0x13 and got the flag.<br />
![lol it didn't load](images/bin4cry/lucky1000/4.png)<br />
