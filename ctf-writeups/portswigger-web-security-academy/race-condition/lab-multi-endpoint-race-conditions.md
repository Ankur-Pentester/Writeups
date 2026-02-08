---
description: >-
  To solve the lab, successfully purchase a Lightweight L33t Leather Jacket. 
  You can log into your account with the following credentials: wiener:peter.
---

# Lab: Multi-endpoint race conditions

First We Login Using Given Credential !!

<figure><img src="../../../.gitbook/assets/image (466).png" alt=""><figcaption></figcaption></figure>

After Login We Visit all The Functionality and Buy Giftcard also  !!

<figure><img src="../../../.gitbook/assets/image (467).png" alt=""><figcaption></figcaption></figure>

We Capture all The Request and Then We Find This Two POST Request `/cart` and `/cart/checkout` and Then Send It To The Repeater !!

<figure><img src="../../../.gitbook/assets/image (468).png" alt=""><figcaption></figcaption></figure>

Then I Send It In Group with Sequence (Single Connection) !!

<figure><img src="../../../.gitbook/assets/image (469).png" alt=""><figcaption></figcaption></figure>

Then I Check The Both Request Response Time First cart Response is `478 millis` and Checkout Request Response is  `243 millis` But If We Want Race Condition We Need Same Response Time That's  Why We Need One Request More That why we add the Homepage Request !!&#x20;

<figure><img src="../../../.gitbook/assets/image (470).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (471).png" alt=""><figcaption></figcaption></figure>

Let's Check again But It's Time Cart and Checkout Request are approx Response Time !!

<figure><img src="../../../.gitbook/assets/image (472).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (473).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (474).png" alt=""><figcaption></figcaption></figure>

Then We add The Giftcard First !!

<figure><img src="../../../.gitbook/assets/image (475).png" alt=""><figcaption></figcaption></figure>

Send The Request in Parallel !!

<figure><img src="../../../.gitbook/assets/image (477).png" alt=""><figcaption></figcaption></figure>

We Check The Browser and We Solve The Lab Successfully !!

<figure><img src="../../../.gitbook/assets/image (476).png" alt=""><figcaption></figcaption></figure>
