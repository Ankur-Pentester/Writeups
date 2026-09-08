# Lab: DOM XSS in document.write sink using source location.search inside a select element

This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search` which you can control using the website URL. The data is enclosed within a select element.

To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the `alert` function.

***

According To The Question We have a Stock Checker Function !!

<figure><img src="../../../.gitbook/assets/image (798).png" alt=""><figcaption></figcaption></figure>

We Find The Vulnerable Stock Function and `StoreId` Parameter !!

<figure><img src="../../../.gitbook/assets/image (799).png" alt=""><figcaption></figcaption></figure>

Then I Test The `StoreId` parameter and Its Working Well , It's Time To make The Xss payload !!

<figure><img src="../../../.gitbook/assets/image (800).png" alt=""><figcaption></figcaption></figure>

## Browser What DO?

* option close
* select close
* image create
* image fail
* onerror execute 💥

```
</option></select><img src=x onerror=alert(1)>
```

<figure><img src="../../../.gitbook/assets/image (801).png" alt=""><figcaption></figcaption></figure>
