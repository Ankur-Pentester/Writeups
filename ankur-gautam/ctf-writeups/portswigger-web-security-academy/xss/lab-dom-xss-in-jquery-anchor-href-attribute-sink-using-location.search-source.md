# Lab: DOM XSS in jQuery anchor href attribute sink using location.search source

This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's `$` selector function to find an anchor element, and changes its `href` attribute using data from `location.search`.

To solve this lab, make the "back" link alert `document.cookie`.

***

According to the to the question vulnerability in the submit feedback page .

<figure><img src="../../../.gitbook/assets/image (806).png" alt=""><figcaption></figcaption></figure>

If a JavaScript library such as jQuery is being used, look out for sinks that can alter DOM elements on the page. For instance, jQuery's `attr()` function can change the attributes of DOM elements. If data is read from a user-controlled source like the URL, then passed to the `attr()` function, then it may be possible to manipulate the value sent to cause XSS. For example, here we have some JavaScript that changes an anchor element's `href` attribute using data from the URL:

```
$(function() {
	$('#backLink').attr("href",(new URLSearchParams(window.location.search)).get('returnUrl'));
});
```

You can exploit this by modifying the URL so that the `location.search` source contains a malicious JavaScript URL. After the page's JavaScript applies this malicious URL to the back link's `href`, clicking on the back link will execute it:

```
?returnUrl=javascript:alert(document.domain) 
```



<figure><img src="../../../.gitbook/assets/image (807).png" alt=""><figcaption></figcaption></figure>

I Try Some Redirect Using back Button in Url !!

<figure><img src="../../../.gitbook/assets/image (808).png" alt=""><figcaption></figcaption></figure>

Redirect Works !!

<figure><img src="../../../.gitbook/assets/image (809).png" alt=""><figcaption></figcaption></figure>

You can exploit this by modifying the URL so that the `location.search` source contains a malicious JavaScript URL. After the page's JavaScript applies this malicious URL to the back link's `href`, clicking on the back link will execute it:

```
?returnUrl=javascript:alert(9) 
```

<figure><img src="../../../.gitbook/assets/image (810).png" alt=""><figcaption></figcaption></figure>
