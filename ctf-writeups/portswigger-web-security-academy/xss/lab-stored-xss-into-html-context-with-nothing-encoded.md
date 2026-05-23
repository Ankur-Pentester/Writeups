# Lab: Stored XSS into HTML context with nothing encoded

This lab contains a stored cross-site scripting vulnerability in the comment functionality.

To solve this lab, submit a comment that calls the `alert` function when the blog post is viewed.

***

We Have a Comment Section in Web Page !

<figure><img src="../../../.gitbook/assets/image (788).png" alt=""><figcaption></figcaption></figure>

Now We Use The Devlopers Tools and Notice Comment is passing Through \<p> tag Now We Can Inject Our Payload in Comment Section !!

<figure><img src="../../../.gitbook/assets/image (790).png" alt=""><figcaption></figcaption></figure>

We Got The POP Up after Injecting The XSS payload in Comment Section !

<figure><img src="../../../.gitbook/assets/image (792).png" alt=""><figcaption></figcaption></figure>
