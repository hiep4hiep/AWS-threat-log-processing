# AWS-threat-log-processing
AWS serverless system to analyze and response to threat logs  Palo Alto Networks


<img src="https://github.com/hiep4hiep/AWS-threat-log-processing/blob/master/topo.png?raw=true">

At current release, as an inline threat prevention service, Prisma Access behave with the unknown threat by Wildfire analysis. So when  an user download a file, Prisma Access passes the file to user while let Wildfire scan the file for threat verdict, then Primsa will get signature to block the file later if it is identified as malware.

Some of my customers require Prisma Access to hold the file until Wildfire identifies it as clean/benign, or block it if the verdict is malware. Unfortunately, this feature is not supported by default (because Prisma Access is inline SASE, it scan the stream, not proxy it). 

The purpose of this project is to add the capability of holding file and wait for verdict to Prisma Access. It will be done  off the box by leveraging serverless architecture on AWS. In short:

1. Prisma Access  will be configured to block all risky file types with File Blocking, and record URL of the downloading file. It will have a security policy to allow user connect to a EDL of clean URL that will be updated after wildfire finish analyzing the file.

2. Log from Cortex Data Lake will be send to Logstash instance on AWS to handle. Logstash will filter  only URL filtering log containing "Content Type" field of "application/pdf" and "application/pe" or any other file type that Primsa Access can handle.

3. Logstash SQS output plugin will then send raw logs to AWS SQS. SQS is configured to trigger Lambda function with event body is the raw log that it received. So everytime it gets a log, one Lambda function is triggered to process that specific log.

4. Lambda function will be do the main jobs:
- Parse log
- Send the URL to Wildfire  to scan and wait for verdict
- If verdict is clean/benign, Lambda will send an email to notify user that the file is ready to download and Lambda also updates EDL (that is hosted on S3 😄)
- If verdict is malicious, Lambda will send an email to inform user that the file is malicious


In this github repo, I put all the configuration file and sample code to make it works, including:
- Logstash configuration
- SQS standard queue name, batch size is 1: "prisma"
- Lambda function: "wildfire-check"
