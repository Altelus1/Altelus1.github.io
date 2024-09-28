---
layout: page
title: Super Secure Static Website
category: Web
type: ctf_rootcon18quals
desc: Super Secure Static Website 100 pts
img-link: /writeups/ctf/rootcon18quals/images/bin4cry/beelzebub/img1.png
---


# [Web] Super Secure Static Website - 100 Pts.
<br />
We're given link to `https://admin-panel.pwndemanila.ph`. Below is the landing page.<br />
![lol it didn't load](images/web/super secure static website/img1.png)<br />
When the `/password` is visited, it gives **405 Method not allowed**<br />
![lol it didn't load](images/web/super secure static website/img2.png)<br />
Below what looks like if we access a nonexisting endpoint.<br /> 
![lol it didn't load](images/web/super secure static website/img4.png)<br />
After a bit of googling, they turned out to be AWS bucket error HTTP responses indicating that the website is an S3 bucket. A stackoverflow answer below helped with the recon:<br />
![lol it didn't load](images/web/super secure static website/img5.png)<br />
![lol it didn't load](images/web/super secure static website/img6.png)<br />
With this, we tried accessing and do basic recon on the website using AWS cli.<br />
![lol it didn't load](images/web/super secure static website/img7.png)<br />
The above check has "access denied" response. We have done some more recon with the AWS cli with no success and the next command we tried returned something. The command is `s3api list-objects-version`.<br />
![lol it didn't load](images/web/super secure static website/img8.png)<br />
After a bit of research again, turns out that these are versions of the file hosted on the website. We examined the metadata and the one that stood out on the output is the file size between different versions of `script.js`<br />
![lol it didn't load](images/web/super secure static website/img9.png)<br />
One of the `script.js` is 522 in size meaning there is more data in there. We tried downloading the file through the command below:<br />
![lol it didn't load](images/web/super secure static website/img10.png)<br />
It successfully downloaded! Viewing the file content, the flag is inside.<br />
![lol it didn't load](images/web/super secure static website/img11.png)<br />
