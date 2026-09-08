# Lab: Web cache poisoning with an unkeyed cookie

This lab is vulnerable to web cache poisoning because cookies aren't included in the cache key. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes alert(1) in the visitor's browser.

***

Let's Start !!

<figure><img src="../../../.gitbook/assets/image (772).png" alt=""><figcaption></figcaption></figure>

First We Capture The Home Request For Testing !!

<figure><img src="../../../.gitbook/assets/image (773).png" alt=""><figcaption></figcaption></figure>

Let's Solve The Lab Using XSS !!

Payload : - `fehost=someString"-alert(1)-"`

<figure><img src="../../../.gitbook/assets/image (774).png" alt=""><figcaption></figcaption></figure>

Refresh The Home Page In The Browser !!<br>

<figure><img src="../../../.gitbook/assets/image (776).png" alt=""><figcaption></figcaption></figure>
