---
description: >-
  To solve the lab, perform a NoSQL injection attack that causes the application
  to display unreleased products.
---

# Lab: Detecting NoSQL injection

We Have This Options !!

<figure><img src="../../../.gitbook/assets/image (450).png" alt=""><figcaption></figcaption></figure>

Then I Click `Accessories` Options and I got Only Three Products and We Got Interesting `Category` Parameters !!

<figure><img src="../../../.gitbook/assets/image (451).png" alt=""><figcaption></figcaption></figure>

submit a `'` character in the category parameter. Notice that this causes a JavaScript syntax error. This may indicate that the user input was not filtered or sanitized correctly.

<figure><img src="../../../.gitbook/assets/image (452).png" alt=""><figcaption></figcaption></figure>

Then We Use  OR Operators `||`  and Use Boolean Based Payload if 1 is equal to 1 or `'1'=='1'`Then This Is always We True and They Return All Products !!

<figure><img src="../../../.gitbook/assets/image (453).png" alt=""><figcaption></figcaption></figure>
