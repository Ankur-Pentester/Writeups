# Lab: Targeted web cache poisoning using an unknown header

This lab is vulnerable to web cache poisoning. A victim user will view any comments that you post. To solve this lab, you need to poison the cache with a response that executes `alert(document.cookie)` in the visitor's browser. However, you also need to make sure that the response is served to the specific subset of users to which the intended victim belongs.

***

Let's Start  !

First Capture The Request of The Home Page !!

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

We Got The Web Cache Server Response !

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Then We Use The Burp Extension Right Click On The Request and Select Extension !

```
Extension > Param Miner > guess headers
```

We Find The `X-host` Header !

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Send The Request and Add The `X-Host: evil.com`  Header and We Got The Response evil.com in Response Tab . It's Mean `X-HOST` header is Working Good !!

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

We Create The Malicious Request in Exploit Server !!

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

We also Add The Comment Using JavaScript Image source Function !!

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Send The Request Again Using The exploit Server Domain !!

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Check The Log's off The Server and We Got The Victim User-Agent Header !!

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Then Add The Victim User-Agent in The Request and Send The Request !!&#x20;

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Victim Got The alert and We Solve The Lab !!

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
