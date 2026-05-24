# Lab: DOM XSS in innerHTML sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search blog functionality. It uses an `innerHTML` assignment, which changes the HTML contents of a `div` element, using data from `location.search`.

To solve this lab, perform a cross-site scripting attack that calls the `alert` function.

***

We Have a search blog functionality in Web Page !

<figure><img src="../../../.gitbook/assets/image (802).png" alt=""><figcaption></figcaption></figure>

After inputting the test payload, we use the debugger in Developer Tools and find that the data is being passed to `innerHTML`.

<figure><img src="../../../.gitbook/assets/image (803).png" alt=""><figcaption></figcaption></figure>

The application takes user-controlled input from `window.location.search` and inserts it into the DOM using `innerHTML`, which is a dangerous HTML sink. The payload `<img src=x onerror=alert(1)>` creates an invalid image, triggering the `onerror` event and executing JavaScript in the victim's browser, confirming a DOM-based XSS vulnerability.

```
<img src=x onerror=alert(1)>
```

<figure><img src="../../../.gitbook/assets/image (804).png" alt=""><figcaption></figcaption></figure>

We Finally Solve The Lab !

<figure><img src="../../../.gitbook/assets/image (805).png" alt=""><figcaption></figcaption></figure>
