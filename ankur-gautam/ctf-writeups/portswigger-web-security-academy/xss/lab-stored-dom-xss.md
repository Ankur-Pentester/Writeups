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

In an attempt to prevent XSS, the website uses the JavaScript `replace()` function to encode angle brackets. However, when the first argument is a string, the function only replaces the first occurrence. We exploit this vulnerability by simply including an extra set of angle brackets at the beginning of the comment. These angle brackets will be encoded, but any subsequent angle brackets will be unaffected, enabling us to effectively bypass the filter and inject HTML.

<figure><img src="../../../.gitbook/assets/image (817).png" alt=""><figcaption></figcaption></figure>
