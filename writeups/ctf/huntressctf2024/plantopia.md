---
layout: page
title: Plantopia
category: Web
type: ctf_huntressctf2024
desc: Plantopia
img-link: /writeups/ctf/huntressctf2024/img/plantopia/1.png
---


# [Web] Plantopia
The following landing page can be seen:<br />
![lol it didn't load](img/plantopia/1.png)<br />
With the credentials given through the challenge description, we get to see the list of plants.<br />
![lol it didn't load](img/plantopia/2.png)<br />
We are also curious about the login session data used.<br />
![lol it didn't load](img/plantopia/3.png)<br />
It's a base64 and the decoded string is: <br />
![lol it didn't load](img/plantopia/4.png)<br />
It seems a very simple structure of a session data. What if we set the 0 value to 1?<br />
![lol it didn't load](img/plantopia/5.png)<br />
![lol it didn't load](img/plantopia/6.png)<br />
We suddenly got an "Admin" button on the dashboard! Checking it out, we can see that it lets us select a plant. Aside from the Admin page, we also have "API Docs" button and it sends us to the Swagger UI.<br />
![lol it didn't load](img/plantopia/7.png)<br />
As can be seen above, there are numerous API endpoints. However, there's only two that's very important for us: (1) /api/admin/settings<br />
![lol it didn't load](img/plantopia/8.png)<br />
It looks like the alert\_command is an actual bash command. Another important API endpoint is (2) /api/admin/sendmail.<br />
![lol it didn't load](img/plantopia/9.png)<br />
Befor we can use these APIs, we should set an API key first. The modified base64 blob that we had earlier can be used.<br />
![lol it didn't load](img/plantopia/10.png)<br />
In the /api/admin/settings, we replaced the `sendmail` command with a reverse shell.<br />
![lol it didn't load](img/plantopia/11.png)<br />
However, we received an error that there should be a "/usr/sbin/sendmail" on the payload.<br />
![lol it didn't load](img/plantopia/12.png)<br />
So we updated it to the following:<br />
![lol it didn't load](img/plantopia/13.png)<br />
It got accepted!<br />
![lol it didn't load](img/plantopia/14.png)<br />
We can then send request to /api/admin/sendmail to trigger the execution. And we got a root shell!<br />
![lol it didn't load](img/plantopia/16.png)<br />
We can find the flag on /srv/app.<br />
![lol it didn't load](img/plantopia/17.png)<br />

