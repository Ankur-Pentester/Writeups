---
description: >-
  This lab contains a vulnerability that enables you to read arbitrary files
  from the server. To solve the lab, retrieve the contents of /etc/passwd within
  10 minutes.
---

# Lab: Discovering vulnerabilities quickly with targeted scanning

Let's Start !!

<figure><img src="../../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

Let's Check The Product an Capture The Request In Burp !!

<figure><img src="../../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

Right Click On Request and Click On Active Scan !!

<figure><img src="../../../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

Go To The Active Scan and Check The High Impact Request and Send It To The Burp !!

<figure><img src="../../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

Then We Use These Payload From Github !!

```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/>
```

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XXE%20Injection/README.md#classic-xxe" %}

<figure><img src="../../../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

Then We Solve The Lab !!

<figure><img src="../../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

