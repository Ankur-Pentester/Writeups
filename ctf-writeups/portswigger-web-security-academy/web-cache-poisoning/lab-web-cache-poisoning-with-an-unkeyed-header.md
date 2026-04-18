# Lab: Web cache poisoning with an unkeyed header

This lab is vulnerable to web cache poisoning because it handles input from an unkeyed header in an unsafe way. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes `alert(document.cookie)` in the visitor's browser.

***

Hacker

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Hacker

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Hacker

<figure><img src="../../../.gitbook/assets/image (601).png" alt=""><figcaption></figcaption></figure>
