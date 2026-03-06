<h1>SOC168 – Whoami Command Detected in Request Body</h1>


<h2>Description</h2>
This lab reviews a possible CMDi payload alert triggered by SOC168 – Whoami Command Detected in Request Body. According to the security alert, the HTTP POST request appears to contain whoami in the request body. The whoami command is a basic OS command that tells you which user account is currently running the command, and by itself it is harmless. However, attackers tend to use this command and various others for probing exercises to see if a web application is vulnerable to CMDi. These types of user inputs should be scrutinized, sanitized, and investigated in order to prevent a misconfigured server-side shell from interpreting and executing them. Let’s analyze the alert and take a closer look at the potential impact.
<br />


<h2> Utilities Used</h2>

- <b>LetsDefend SIEM</b> 
- <b>LetsDefend Email Security</b>
- <b>LetsDefend EDR</b>
- <b>VirusTotal</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (22H2)

<h2>Alert walk-through:</h2>

<p align="center">
Go to investigation channel in Monitoring to get the context of the alert: <br/>
<img src="https://i.imgur.com/DaiipN3.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
After taking ownership of the case, very simply look at the rule name and examine the direction of the traffic thats occuring between both devices. This is inbound traffic. An HTTPS POST request being sent from an external IP into an Internal server. :  <br/>
<img src="https://i.imgur.com/CVMCejd.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/h2iqLaq.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/zjRx2t8.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
CHINANET is the owner. This is inbound traffic. Based off of VT details and relations its most likely a static IP assigned by an ISP to an endpoint. IP address is listed has a -4 community score. Traffic is not coming from company network, its external. Hostname: WebServer1004. The owner is webadmin3. Last login was on Feb, 28, 2022, 11:11 AM <br/>
<img src="https://i.imgur.com/vteJtIy.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/QN2SgqT.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/yo3Tltq.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/M6d8LWA.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
Now we'll examine the CMDi payloads embedded in the Request body:  <br/>
<img src="https://i.imgur.com/RjCFTPV.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
When analyzing the raw logs in Log Management we see alot of different CMDi recon/probing payloads being deployed to the server.  <br/>
<img src="https://i.imgur.com/OD3dBo1.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/9gjkD3a.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/fVbDmsx.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/sqzs3ID.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
The traffic was malicious because it contained the CMDi payloads:  <br/>
<img src="https://i.imgur.com/sCujGob.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was CMDi:  <br/>
<img src="https://i.imgur.com/u1dOxvU.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Email Security was checked to determine whether this alert was triggered by a scheduled pen test or a security simulation. After searching the inbox by the hostname, IP address, and username, no emails were found validating this activity as planned work:  <br/>
<img src="https://i.imgur.com/TowIxaS.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/CmGGKvf.jpeg" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was an inbound HTTP request. Internet to Company Network:  <br/>
<img src="https://i.imgur.com/h3wEizW.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
check if the attack was successful:  <br/>
<img src="https://i.imgur.com/toHZfZt.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
The CMDi payloads requests were allowed by the device, after reviewing the web server’s endpoint security data (i.e. Processes and Terminal History) post delivery exploitation was successful. The server-side shell interpreted and executed attacker-controlled input : <br/>
<img src="https://i.imgur.com/ARjCN1z.jpeg" height="60%" width="60%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/YkLg5Jl.jpeg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />
<br />
Attack was successful. :  <br/>
<img src="https://i.imgur.com/ClHmwIZ.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Contain the users device :  <br/>
<img src="https://i.imgur.com/2dJovAa.jpeg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/yPZvkEf.jpeg" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />
<br />
Add the artifacts that were examined in the investigation :  <br/>
<img src="https://i.imgur.com/W1bEYPt.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was an succesful attack, so escalation is needed :  <br/>
<img src="https://i.imgur.com/wjPCpa8.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
write your analyst notes from the case. click confirm and youre finished! Next closing the case..:  <br/>
<img src="https://i.imgur.com/ivQuHnQ.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Lastly, the alert triggered was a True Positive because the security system correctly identified this as unplanned malicious XSS payloads being delivered to the web server :  <br/>
<img src="https://i.imgur.com/zGqFIBS.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
