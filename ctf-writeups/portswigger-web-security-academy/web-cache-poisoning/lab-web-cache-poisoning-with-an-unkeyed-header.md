# Lab: Web cache poisoning with an unkeyed header

This lab is vulnerable to web cache poisoning because it handles input from an unkeyed header in an unsafe way. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes `alert(document.cookie)` in the visitor's browser.

***

Let's Start The Lab&#x20;

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

We Capture The Home Directory First !!

<figure><img src="../../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

We Use `Param Miner` Extensions From `BApp  Store` in `Burpsuite`  For Identify Auto Request Headers !!&#x20;

<figure><img src="../../../.gitbook/assets/image (760).png" alt=""><figcaption></figcaption></figure>

We Use The Extension !!

<figure><img src="../../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Go In Target and Select The Host and Check The Extension Result and We Got a `X-Forwarded-Host` This Header !!

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

We Create The malicious Request using The Exploit Server !!

<figure><img src="../../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Add a cache-buster query parameter, such as `?cb=nexxelsecurity`&#x20;

and we also add The attacker domain where we serve the malicious Request !!

<figure><img src="../../../.gitbook/assets/image (761).png" alt=""><figcaption></figcaption></figure>

Then We Go on the our cache-buster query parameter and boom our payload is work it's mean there is a cache poisoning vulnerability !!

<figure><img src="../../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Then Let's Solve The  Lab sending The Request in Home Directory !!

<figure><img src="../../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

We Same The Send Just Change The Directory TO Home !!

<figure><img src="../../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Boom We Solve The Lab !!

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>
