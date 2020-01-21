---
title: Kerberoast Attack Techniques
description: >-
  In this blog we will focus on Kerberoast attack techniques (Old Technique and
  New Technique). I will try to cover the basics about Kerberos…
image: images/blog/KR.png
date: '2020-1-14T23:49:15.728Z'
categories: []
keywords: []

---

In this blog we will focus on Kerberoast attack techniques (Old Technique and New Technique). I will try to cover the basics about Kerberos protocol and then we will see the attacking techniques from a penetration testing perspective.

#### What is Kerberos?

Kerberos is designed to provide authentication of user identity in a networked computing environment consisting of workstations and servers.

**Kerberos in a Nutshell.** 

The Kerberos authentication system is built on top of tickets served by KDC. The core idea behind Kerberos is that the users don’t share account passwords to each service they want to use. Instead, they share a ticket which they get from KDC.

When a user first starts using the system, they will use their password to get a master ticket called a TGT (**ticket-granting ticket**). This master ticket expires in 25 hours, after which, the user will need to enter the password again to get another one.

When the user needs service access, that uses Kerberos, they will show that master ticket (TGT) to the Kerberos server and get a ticket specifically for that service. Then, shows the ticket just for that service to the service to prove who you are.

**Steps in Kerberos Authentication** 

>  *1\. Password converted to **NTLM hash**, a timestamp is encrypted with the hash and sent to the KDC as an authenticator in the authentication ticket (TGT) request (AS-Request).*

> *2\. The **Domain Controller** (KDC) checks user information & creates **Ticket-Granting Ticket** (TGT).*

> *3\. The TGT is encrypted, signed, & delivered to the user (AS-Reply). Only the Kerberos service (**KRBTGT**) in the domain can open and read TGT data.*

> *4\. The User presents the TGT to the DC when requesting a **Ticket Granting Service** (TGS) ticket (TGS-Request). The data in the TGT is effectively copied to create the TGS ticket.* 

> *5\. The TGS is encrypted using the target service accounts’ NTLM password hash and sent to the user (TGS-Reply).*

> *6\. The user connects to the server hosting the service on the appropriate port & presents the TGS. The service opens the TGS ticket using its NTLM password hash.*

**Kerberos Attacks:**

There are several different types of Kerberos attacks ranging from recon (SPN Scanning), to offline service account password cracking (Kerberoast), to persistence (Silver & Golden Tickets).

> Here are the most popular AD Kerberos attacks: 

> > 1\. **SPN Scanning** — finding services by requesting service principal names of a specific SPN class/type.  
> > 2\. **Silver Ticket** — forged Kerberos TGS service ticket  
> > 3\. **Golden Ticket** — forged Kerberos TGT authentication ticket  
> > 4. **MS14–068 Forged PAC Exploit** - exploitation of the Kerberos vulnerability on Domain Controllers.  
> > Etc.

Now, let’s see how we can leverage the Kerberos implementation to our advantage.

#### Old Technique

We will see and understand the old technique first (i.e. SPN Scanning and then cracking the tickets).

In general, we follow the process below:

1\. Enumerate the domain accounts with SPNs set- either with GetUserSPNS.ps1 script from PowerView’s or Impacket’s “GetUserSPN.py“.  

2\. Request TGSs for these specific SPNs with the built-in Windows tool setspn.exe.  

3\. Extract these tickets from memory by invoking the kerberos::list /export Mimikatz command, with the optional base64 export format set first. The tickets were then downloaded, or the base64-encoded versions pulled down to the attacker’s machine and decoded. (Note: You don’t need admin rights to execute the command :))  

4\. Begin offline password cracking with “tgsrepcrack.py”, or John whit the help for kirbi2john.py.

Let’s see the Demo :)

We can scan the services with windows built-in utility. I have used in-built utility (i.e setspn.exe).

![“setspn.exe” output](/images/blog/1__eV3JOkNM0ez5wyxqO__2AfA.png)
“setspn.exe” output

Now, if you notice we have “CN= Computers” and “CN=Users” for listed service accounts. We will be focusing on “CN=Users” as these are user generated and so we can try to crack :).

![](/images/blog/1__9sdOqlsakZ__WlzlYBCiOBw.png)

Now that we know the service accounts which we will be cracking or trying to crack, let’s go ahead and request Kerberos tickets for specific service accounts.

> **Command**: Add-Type -AssemblyName System.IdentityModel  
> New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken –ArgumentList “SPN Name”

![Powershell Command (Non Admin User)](/images/blog/1__1pyTys__V3wYA4x3FJAwDXg.png)
Powershell Command (Non Admin User)

Now, we have tickets in memory. We will use Mimikatz to export the tickets from memory. This is one of the down side of this method as you are running Mimikatz this might trigger Alert or this can be detected by AV’s.

Note: You can also load Mimikatz into memory using PowerShell  
“IEX (New-Object Net.WebClient).DownloadString” feature)

> **Command:** Invoke-Pwc -Command ‘“kerberos::list /export” exit”’

![Export Ticket from Memory](/images/blog/1____OEUj3L7XVFHiUezzPPaVw.png)
Export Ticket from Memory![Extracted Tickets](/images/blog/1__pVBtLvCx8qeYC8my6GHZIw.png)
Extracted Tickets

We have successfully extracted the tickets from memory. Can we crack these tickets??  
There are multiple ways to try this. Let’s see how we can leverage tgsrepcrack.py form Kerberoast toolkit.

1 > Using Kerberosast: Tgsrepcrack.py

We have provided the wordlist to crack the kirbi file

> **Command:** C:\\Users\\pratik\\Desktop\\kerberoast>python tgsrepcrack.py dict.txt “Ticket.kirbi”

![Cracked Ticket](/images/blog/1__clSmimsPL3AqYOlYfiL__sg.png)
Cracked Ticket

:) Cracked

2> Convert .kirbi file to John format

Now, we will use John to crack the tickets. We know that tickets are in kirbi format so first we will convert the ticket to John format. We can use Kerberoast (kirbi2john.py) for the same.

![John format](/images/blog/1__bE1vtiRaTiJtutR2vaTWew.png)
John format

> **Command:**./john –format=krb5tgs crack\_file — wordlist=dict.txt

![Cracked using John](/images/blog/1__COgpebl9Pj0CFvI9XCMyYA.png)
Cracked using John

Cracked :)

#### New Technique

HarmJ0y has written a good blog on kerberoasting without Mimikatz. This technique is pretty straight forward and simpler than the old technique :)

What you need is “Invoke-Kerberoast.ps1” and then you are good to go :)  
To crack the tickets, first import “.ps1” module.

This will request the associated TGS Tickets in john or hashcat crackable format :)

![Invoke-Kerberoast](/images/blog/1__6hOtKGmLQB__bbHkxmV54JQ.png)
Invoke-Kerberoast

Crack the tickets using John

![Cracked using John](/images/blog/1__brqC58Ztu1P__ja6un6HNoQ.png)
Cracked using John

Feel free to submit your feedback and comments :)