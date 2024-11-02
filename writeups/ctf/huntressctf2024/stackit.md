---
layout: page
title: Stack It
category: Reversing
type: ctf_huntressctf2024
desc: Stack It
img-link: /writeups/ctf/huntressctf2024/img/stack_it/1.png
---


# [Reversing] Stack It
The following challenge description has been given:<br />
![lol it didn't load](img/stack_it/1.png)<br />
Below is the file type:<br />
![lol it didn't load](img/stack_it/1.5.png)<br />
Opening up Ghidra and analyzing the stack\_it.bin, below is what we concluded.<br />
![lol it didn't load](img/stack_it/2.png)<br />
The initialized two byte arrays were identified and extracted the hex contents.<br />
Byte array 1:<br />
![lol it didn't load](img/stack_it/3.png)<br />
Byte array 2:<br />
![lol it didn't load](img/stack_it/4.png)<br />
We then XOR them together and got the flag. The following code has been used:
<br />
```python
with open("1.txt", "r") as rf:
    one = rf.read().strip().split("\n")

with open("2.txt", "r") as rf:
    two = rf.read().strip().split("\n")

print("flag{",end="")
for i in range(len(one)):
    print(chr(int(one[i],16) ^ int(two[i],16)),end="")
print("}")
```
<br />![lol it didn't load](img/stack_it/5.png)<br />
![lol it didn't load](img/stack_it/6.png)<br />
