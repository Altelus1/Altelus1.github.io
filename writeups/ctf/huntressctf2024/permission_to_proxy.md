---
layout: page
title: Permission to Proxy
category: Misc
type: ctf_huntressctf2024
desc: Permission to Proxy
img-link: /writeups/ctf/huntressctf2024/img/y2j/1.png
---


# [Misc] Permission to Proxy
The following landing page can be seen:<br />
![lol it didn't load](img/permission_to_proxy/1.png)<br />
We have this page below:<br />
![lol it didn't load](img/permission_to_proxy/2.png)<br />
We can quickly confirm that we can use this portal to proxy our HTTP requests.<br />
![lol it didn't load](img/permission_to_proxy/3.png)<br />
We don't really have a clear target where we can use the proxy server. However, we can target itself and see what we can obtain.<br />
![lol it didn't load](img/permission_to_proxy/4.png)<br />
It returned 503 however, we can see that it has a long hostname through the `Via` header.<br />
We can use this hostname to try again to proxy to itself again.<br />
![lol it didn't load](img/permission_to_proxy/5.png)<br />
This time, we can see the IP address `10.120.2.92` (This may be different each time the instance is spawned). We can try to internally scan for open ports with the IP address above.<br />
![lol it didn't load](img/permission_to_proxy/6.png)<br />
A couple of ports have been seen opened. We inspected each of those but the one that's remarkable is the port 50000. Below is the output if we try to access it via proxy.<br />
![lol it didn't load](img/permission_to_proxy/7.png)<br />
A directory listing of the / directory! We can step through /home<br />
![lol it didn't load](img/permission_to_proxy/8.png)<br />
...Then through /home/user/...<br />
![lol it didn't load](img/permission_to_proxy/9.png)<br />
...Then through /home/user/.ssh. And there's id_rsa! We can try grabbing it<br />
![lol it didn't load](img/permission_to_proxy/10.png)<br />
We grabbed it!<br />
![lol it didn't load](img/permission_to_proxy/11.png)<br />
We can then try to use it to login to server. We can use the proxy server and set it in the Proxychains via `/etc/proxychains4.conf`<br />
![lol it didn't load](img/permission_to_proxy/12.png)<br />
We tried to login to the SSH and it's successful.<br />
![lol it didn't load](img/permission_to_proxy/13.png)<br />
For privilege escalation, we tried finding SUID binaries:<br />
![lol it didn't load](img/permission_to_proxy/14.png)<br />
There's a suid on `/bin/bash`! We executed it and gained root privileges. With it. we got the flag in the /root directory.<br />
![lol it didn't load](img/permission_to_proxy/15.png)<br />

