---
description: >-
  To solve the lab, modify your session token to gain access to the admin panel
  at /admin, then delete the user carlos.
---

# Lab: JWT authentication bypass via unverified signature

First We Login Using Given Credential and send the request in repeater tab   !!

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Then We Install The The Extenstion Jwt editor from Bapp Store and Then we click on json web token !!

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Then We Access The `/admin` endpoint but we can't access that endpoin only administrator user can access !!

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Then We Change The username to `administrator` !!

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

We Change The username to administrator in this endpoint `/my-account?id=administrator` and we also change the username in the jwt token and we send the request and we access the administrator account !!

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Then We Copy The Temper JWT Token and Paste In The Storage Tab and Reload The Page and Now We access The administrator account !!

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

After Delete The Carlos User We automatic Complete The Lab !!

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
