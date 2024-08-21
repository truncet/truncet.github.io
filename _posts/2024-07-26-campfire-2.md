---
layout: post
title: Campfire-2 
subtitle: Hack The Box -Sherlock
cover-img: /assets/img/htb.png
tags: [htb, sherlock, campfire-2]
author: truncet
---
{: .box-note}
**Note:** This was done in window vm in ubuntu.


## Forela's Network is constantly under attack. The security system raised an alert about an old admin account requesting a ticket from KDC on a domain controller. Inventory shows that this user account is not used as of now so you are tasked to take a look at this. This may be an AsREP roasting attack as anyone can request any user's ticket which has preauthentication disabled.

{: .box-note}
As this is related to **AsREP roasting attack**,  more information on what this attack is and how to perform it can be found  [here](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/asreproast#asreproast)

There is Security.evtx file provided. After I unzipped it, I used zimmermans [EvtxECmd](https://ericzimmerman.github.io/) to get csv files from the .evtx file. I ran the following command.

    mkdir 
    EvtxECmd.exe -f Security.evtx --csv .\output
    
This will create a csv file with all the information making it easier to view even in linux systems. 

#### 1) When did the ASREP Roasting attack occur, and when did the attacker request the Kerberos ticket for the vulnerable user?

**&rArr;** We can see the first kerberos request without pre-authorization was made in **2024-05-29 06:36:40**, EventId 4768.


#### 2) Please confirm the User Account that was targeted by the attacker.

**&rArr;** From the PayloadData1 from the same row, Target: forela.local\arthur.kyle, we can see that the user targeted was **arthur.kyle**

#### 3) What was the SID of the account?

**&rArr;** From the last column of the same row, 

    {"@Name":"TargetSid","#text":"S-1-5-21-3239415629-1862073780-2394361899-1601"}

This indicates that the SID is **S-1-5-21-3239415629-1862073780-2394361899-1601**


#### 4) It is crucial to identify the compromised user account and the workstation responsible for this attack. Please list the internal IP address of the compromised asset to assist our threat-hunting team.

**&rArr;** From the same column as question 3, we can see that internal ip address and port.

    {"@Name":"IpAddress","#text":"::ffff:172.17.79.129"},{"@Name":"IpPort","#text":"61965"}

Hence, the internal ip is **172.17.79.129** and port 61965.

#### 5) We do not have any artifacts from the source machine yet. Using the same DC Security logs, can you confirm the user account used to perform the ASREP Roasting attack so we can contain the compromised account/s?

**&rArr;** I was confused about this one. From the security events, we can see that the user, we could see that network share was accessed by another user, **happy.grunwald**. Hence, this must be the user used to perform ASREP Roasting attack and trying to do lateral movement. 



