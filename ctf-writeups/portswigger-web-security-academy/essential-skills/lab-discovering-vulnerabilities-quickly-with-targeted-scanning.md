---
description: >-
  This lab contains a vulnerability that enables you to read arbitrary files
  from the server. To solve the lab, retrieve the contents of /etc/passwd within
  10 minutes.
---

# Lab: Discovering vulnerabilities quickly with targeted scanning

Let's Start !!

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Let's Check The Product an Capture The Request In Burp !!

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Right Click On Request and Click On Active Scan !!

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Go To The Active Scan and Check The High Impact Request and Send It To The Burp !!

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Then We Use These Payload From Github !!

```
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/>
```

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XXE%20Injection/README.md#classic-xxe" %}

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Then We Solve The Lab !!

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

