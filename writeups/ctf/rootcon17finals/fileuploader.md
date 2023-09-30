---
layout: page
title: File uploader
category: Web
type: ctf_rootcon17finals
desc: Web 100 pts
img-link: /writeups/ctf/rootcon17finals/images/fileuploader_1.png
---


# [Web] File Uploader - 100 Pts.
<br />We're given app.py source code with the URL to http://149.28.156.76:31337/ and an upload function can be seen on /upload endpoint.<br />
<br />![lol it didn't load](images/fileuploader_1.png)
<br />We uploaded a file and and intercepted it with burpsuite.
<br />![lol it didn't load](images/fileuploader_2.png)
<br />Checking out the upload function, it seems that it is vulnerable to directory traversal. In burpsuite we can set the filename to any directory by prepending a lot `../`.
<br />![lol it didn't load](images/fileuploader_3.png)
<br />We also have to check out the /flag code. NOTE: The screenshot below is the app.py code before the fix has been applied during the CTF. That may be the case, the idea is still there.
<br />![lol it didn't load](images/fileuploader_4.png)
<br />As can be seen above, the flag is being sent to localhost:31337 through the HTTP header. We can abuse the directory traversal to overwrite `/etc/hosts` file and point it to public server that we have.
<br />![lol it didn't load](images/fileuploader_5.png)
<br />Turns out the web server is running as root as well that's why we were able to overwrite the `/etc/hosts` file.
<br />We can send an HTTP request to the `/flag` endpoint and it should connect to our server with the flag. Looking at our server below...
<br />![lol it didn't load](images/fileuploader_6.png)
<br />We managed to connect the target server to our server through HTTP with the flag in the header.
