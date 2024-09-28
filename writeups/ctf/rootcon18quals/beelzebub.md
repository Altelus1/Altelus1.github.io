---
layout: page
title: Beelzebub
category: BinForCry
type: ctf_rootcon18quals
desc: Heaven 100 pts
img-link: /writeups/ctf/rootcon18quals/images/bin4cry/beelzebub/img1.png
---


# [BinForCry] Beelzebub - 100 Pts.
<br />
We're given with this file.<br />
![lol it didn't load](images/bin4cry/beelzebub/img1.PNG)<br />
Output states that it's an ELF file.
<br />

We loaded it on Ghidra and checked out main function.<br />
![lol it didn't load](images/bin4cry/beelzebub/img2.PNG)<br />
There are a lot of checks going on within the main function however what we want is inside `FUN_001014c0`:<br />
![lol it didn't load](images/bin4cry/beelzebub/img3.PNG)<br />
As can be seen, it prints `RC18{` thus we can say that this function prints the flag. Let's investigate the required parameters.<br />
![lol it didn't load](images/bin4cry/beelzebub/img4.5.PNG)<br />
We can try `beelzebub` as the 3rd parameter by setting RDX register to an address where string `beelzebub` is stored when that function is called in gdb.<br />
We need to find the setting of 0x1b39, 0x1a4, and "ILOVEROOTCON" series of instruction. It can be found starting on `0x55555555527c`.<br />
![lol it didn't load](images/bin4cry/beelzebub/img6.PNG)<br />
By setting the RIP register to 0x555555555279 (I set the RIP register after breaking on the `main` function), it will then setup the parameters required for the print flag function. We then set a break point to the 0x55555555528d, calling `0x5555555554c0` which is the "print flag" function.<br />
![lol it didn't load](images/bin4cry/beelzebub/img9.PNG)<br />
As can be seen, RDX registry makes no sense so we can set it to an address where string "beelzebub" is stores. We know these exists because they are present in the decompiled code. Within the main function, we found it on `0x555555556080`. We set the RDX to that address and continued execution. Upon execution, it printed the flag.<br />
![lol it didn't load](images/bin4cry/beelzebub/img12.PNG)<br />

