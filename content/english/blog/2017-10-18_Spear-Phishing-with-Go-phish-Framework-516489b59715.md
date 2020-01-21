---
title: Spear Phishing with Go phish Framework
image: images/blog/SP.jpg
description: What is Spear Phishing?
date: '2020-01-07T16:21:22.255Z'
categories: []
keywords: []

---

#### What is Spear Phishing?


Spear phishing is an e-mail spoofing fraud attempt that targets a specific organization, seeking unauthorized access to confidential data. Spear phishing attempts are not typically initiated by “random hackers” but are more likely to be conducted by perpetrators out for financial gain, trade secrets or military information.

**Phishing vs Spear Phishing**

Spear-phishing can easily be confused with phishing because they are both online attacks on users that aim to acquire confidential information. The attackers often disguise themselves as a trustworthy entity and make contact with their target via email, social media, and even text messages. Spear-phishing attacks target a specific victim, and messages are modified to specifically address that victim, purportedly coming from an entity that they are familiar with and containing personal information.

Unlike spear-phishing attacks, Phishing attacks are not personalized to their victims, and are usually sent to Groups at the same time. The goal of phishing attacks is to send a spoofed email (or other communication) that looks as if it is from an authentic organization to a large number of people, banking on the chances that someone will click on that link and provide their personal information or download malware.

Now that we know the difference let’s see what are the techniques/ tools are available to perform the Spear-Phishing

Spear-phishing requires more thought and time to achieve than phishing. Spear-phishing attackers try to obtain as much personal information about their victims as possible to make the emails that they send look legitimate and to increase their chance of fooling recipients. Because of the personal level of these emails, it is more difficult to identify spear-phishing attacks than to identify phishing attacks conducted at a wide scale. This is why spear-phishing attacks are becoming more prevalent.

There are multiple open source tools available for Spear Phishing

> 1>Sendmail.rb

> 2> Phishing Frenzy

> 3> [Gophish](https://getgophish.com/)

Case Study and Demo :)

**Gathering Info:**

For better understanding of how this attack works Lets consider a following Example.

**_Scenario:_**

The attacker does research on their targets, find out who they regularly communicate with, and sends a personalized email to the target.

Attacker uses a social engineering techniques to gather more info about target (email id, contact, Bank Name and work location). Then Attacker creates the mirrored website. (Which can be done using tools like SET-ToolKit, HTTrack etc.) As this is blog about phishing, I will not go in depth of website cloning. Attacker will buy domain which has some resemblance with the domain of Victim’s Bank to increase the chance for successful phishing attack.

Next, the attacker creates a fake e-mail with subject “ Account Credentials are about to expire” with legitimate mail body and send the e-mail to Victim stating that his account credentials are about to expire and a link to the Login page (Attacker’s Mirrored Website). Once Victim clicks on the link and submits the credentials, attacker redirects Victim to the legitimate Altoro Bank’s Login page.

_Note :_ To make sure that we succeed in the attack, we have to create really good phishing e-mails which looks legitimate and will make target to click on the link :)

[**Gophish**](https://getgophish.com/)

Attacker will install and configure the Spear phishing attack.

![Running Gophish server](/images/blog/1____e26__yrtOLP1p7JtipovKw.png)
Running Gophish server![Gophish server](/images/blog/1__XAejR2v5Nf3GTFCjK3fdjg.png)
Gophish server

**Creating Mirrored Website**

Mirrored Web Site ( Attacker's Web Server)

![Mirrored Web Site](/images/blog/1__QkB7h00CXYJMOr6Fy6CKRQ.png)
Mirrored Web Site

**Creating Landing Pages**

Once we create the mirrored website, we configure the Landing Page in Gophish

![Phishing Campaign Configuration](/images/blog/1__fqT1wIIsdnKV4W79Ql0__pQ.png)
Phishing Campaign Configuration

**As shown in the above screenshots, Gophish provides the following options**

*   **Import Site**
*   **Capture Data**
*   **Capture Passwords**

**Redirect to: This feature will redirect user to specified URL once the attack is complete**

**Creating Phishing Mail Template**

To increase success rates, these messages often contain urgent explanations on why they need sensitive information. Victims are asked to open a malicious attachment or click on a link that takes them to a spoofed website where they are asked to provide passwords, account numbers, PINs, and access codes. An attacker posing as a friend might ask for usernames and passwords for various websites. In reality, the attackers will use that password, or variations of it, to access different websites that have confidential information such as credit card details or Social Security Numbers.

Gophish has this feature where you can create the mail template, as shown below.

![Creating Email Template](/images/blog/1__PS5O5NGHYAoYcvHVYxcZhA.png)
Creating Email Template

**_Important thing to note here is that we have not mentioned any Name and the exact URL. Rather we have just used {{.FirstName}} and {{URL}}. This modification plays a major role in the attack execution._**

**Users & Groups**

Now, we have setup the website, created mail template. The next thing we have to do is add Users to whom we will be sending the phishing mail. Gophish has capability to add group of users. We can import bulk users from excel file. This is depicted below.

![Users and Groups (Victim details)](/images/blog/1__LR4lXpfAL2N73ngJ4I5qrQ.png)
Users and Groups (Victim details)

**Sending Profile**

We have to configure the sending profile for Gophish. In Sending profile, we provide the details of ‘SMTP’ server and the email id (Attacker email) which we are going to use for Phishing.

Note: We can send the test e-mails to confirm that SMTP Configuration is correct.

![Sending Profile Configuration](/images/blog/1__oTq2ewMgqog2eN__Cf6Ke0w.png)
Sending Profile Configuration

Now that we are all set with the configuration, let’s go ahead and create the phishing Campaign. :)

![Phishing Campaign](/images/blog/1__tZXOal__RUfhBFVlth3__EJg.png)
Phishing Campaign

The below fields are added in the Campaign:

1\. Name of the Campaign

2\. Email Template : This is the same template which we created in this document

3\. Landing Page: This is the link which we have included in our Email Template ( Attacker’s Web site)

4\. URL: This is the most important part of the campaign. This is the go phish listener URL.

5\. Schedule : You can schedule the phishing attack

6\. Sending Profile: This the profile which has details of SMTP server and Attacker’s Email ID

7\. Group Name: Targeted users

Once we provide all the details and click on launch campaign, we are all done and now we are waiting for our victim to open the email and click the link. :)

![Victim’s mail account](/images/blog/1____H13EZ92v6gYJSYH4u6z__w.png)
Victim’s mail account

Gophish will provide us with the status of our Spear Phishing Campaign

**1> Sending Email**

![Gophish phishing campaign status](/images/blog/1__f87yu__61aw__MuTjFOWu__wg.png)
Gophish phishing campaign status

**2> Mail Sent**

![Email Sent to Victim](/images/blog/1__NfCqu__UJokDH0J__78bFSfw.png)
Email Sent to Victim

**3>Victim Clicked the Link**

![](/images/blog/1__Q4Meot8ft__5h__krZmtfFpA.png)

**4> Victim’s captured data**

![Victim’s Credentials Captured](/images/blog/1____KIsXrJ80LluXFGQWV4Wfg.png)
Victim’s Credentials Captured

Gophish also gives you functionality to replay the same credentials :)

This is just an informative blog on Gophish and Spear Phishing attack. There are still so many things that we can tweak and perform different kind of phishing attacks with different tools and different methodologies.