# AWS-threat-log-processing
AWS serverless system to analyze and response to threat logs  Palo Alto Networks


<img src="https://github.com/hiep4hiep/AWS-threat-log-processing/blob/master/topo.png?raw=true">

At current release, as an inline threat prevention service, Prisma Access behave with the unknown threat by Wildfire analysis. So when  an user download a file, Prisma Access passes the file to user while let Wildfire scan the file for threat verdict, then Prims will get signature to block the file later if it is identified as malware.

Some of my customers require Prisma Access to hold the file until Wildfire identifies it as clean/benign, or block it if the verdict is malware. Unfortunately, this feature is not support by default (because Prisma Access is inline SASE, it scan the stream, not proxy it). 
