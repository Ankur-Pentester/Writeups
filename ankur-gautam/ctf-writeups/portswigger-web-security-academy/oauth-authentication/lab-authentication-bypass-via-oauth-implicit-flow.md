# Lab: Authentication bypass via OAuth implicit flow

This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is `carlos@carlos-montoya.net`.

You can log in with your own social media account using the following credentials: `wiener:peter`.

***

First Login In Using Given Credential !!

<figure><img src="../../../.gitbook/assets/image (823).png" alt=""><figcaption></figcaption></figure>

Then Click Continue !

<figure><img src="../../../.gitbook/assets/image (824).png" alt=""><figcaption></figcaption></figure>

While proxying traffic through Burp, click "My account" and complete the OAuth login process. Afterwards, you will be redirected back to the blog website.

<figure><img src="../../../.gitbook/assets/image (825).png" alt=""><figcaption></figcaption></figure>

`access_token` is Leak In URL !

<figure><img src="../../../.gitbook/assets/image (826).png" alt=""><figcaption></figcaption></figure>

Then I Change The Email and username to `carlos`

<figure><img src="../../../.gitbook/assets/image (827).png" alt=""><figcaption></figcaption></figure>

Then I Set The Cookie in Storage Tab and Refresh The Page !!

<figure><img src="../../../.gitbook/assets/image (828).png" alt=""><figcaption></figcaption></figure>
