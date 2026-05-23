# Lab: Reflected XSS into HTML context with nothing encoded

This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

To solve the lab, perform a cross-site scripting attack that calls the `alert` function.

***

We Have a Search Box and Here We Right Test and They Show on The Web Page !

<figure><img src="../../../.gitbook/assets/image (784).png" alt=""><figcaption></figcaption></figure>

Then I Try Some Html Injection To Verify Here is XSS Possible or Not and Then I Use Html Bold Tag and Test Has Been Bold Succesfully !

```
<b>Test</b>
```

<figure><img src="../../../.gitbook/assets/image (785).png" alt=""><figcaption></figcaption></figure>

Then I use XSS `alert()` Function Payload !!

```
<script>alert()</script>
```

<figure><img src="../../../.gitbook/assets/image (786).png" alt=""><figcaption></figcaption></figure>

Lab Has Been Solved Succesfully !

<figure><img src="../../../.gitbook/assets/image (787).png" alt=""><figcaption></figcaption></figure>
