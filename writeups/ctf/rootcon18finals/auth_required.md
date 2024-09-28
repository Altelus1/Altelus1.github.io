---
layout: page
title: Auth Required
category: Web
type: ctf_rootcon18finals
desc: Auth Required
img-link: /writeups/ctf/rootcon18finals/images/web/auth_required/img1.PNG
---


# [Web] Auth Required
We're given with the link `https://auth-required.pwndemanila.ph`<br />
![lol it didn't load](images/web/auth_required/img1.PNG)<br />
When we accessed the `/flag.php`, it asks for username and password as part of HTTP Basic authentication.<br />
![lol it didn't load](images/web/auth_required/img2.PNG# medium)<br />
We tried entering any credentials and it shows us the 500 error message.<br />
![lol it didn't load](images/web/auth_required/img3.PNG)<br />
We tried searching for ".htaccess bypasses" or "Apache 403/401 bypasses" and we came across the Orange Tsai's writeup for some bypasses and exploits within Apache Server [here](https://blog.orange.tw/posts/2024-08-confusion-attacks-en/)<br />
On the `ACL Bypasses`, we found out that we can insert `%3F` on the end of flag.php and append something after it.<br />
![lol it didn't load](images/web/auth_required/img6.png)<br />
Then we tried it on the challenge and we got the flag.<br />
![lol it didn't load](images/web/auth_required/img5.png)<br />
