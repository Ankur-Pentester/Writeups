---
description: >-
  To solve the lab, modify your session token to gain access to the admin panel
  at /admin, then delete the user carlos.
---

# Lab: JWT authentication bypass via flawed signature verification

First We Login Using Given Credential and send the request in repeater tab   !!

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

We Have a Jwt Token Let's Try To Tamer The Session Token !!

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

We use Below  Website and Change `The algorithm to None` and `sub to administrato`r !!

{% embed url="https://www.jwt.io/" %}

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

We Change The username to administrator in this endpoint `/my-account?id=administrator` and we also change the username in the jwt token and we send the request and we access the administrator account !!

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Then We Copy The Temper JWT Token and Paste In The Storage Tab and Reload The Page and Now We access The administrator account !!

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

After Delete The Carlos User We automatic Complete The Lab !!

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
