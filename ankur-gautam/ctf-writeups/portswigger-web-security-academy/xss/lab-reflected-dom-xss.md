# Lab: Reflected DOM XSS

This lab demonstrates a reflected DOM vulnerability. Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.

To solve this lab, create an injection that calls the `alert()` function.

***

open the `searchResults.js` file and notice that the JSON response is used with an `eval()` function call.

<figure><img src="../../../.gitbook/assets/image (818).png" alt=""><figcaption></figcaption></figure>

By experimenting with different search strings, you can identify that the JSON response is escaping quotation marks. However, backslash is not being escaped.

<figure><img src="../../../.gitbook/assets/image (819).png" alt=""><figcaption></figcaption></figure>

<pre><code><strong>Payload :-  \"-alert(1)}//
</strong></code></pre>

As you have injected a backslash and the site isn't escaping them, when the JSON response attempts to escape the opening double-quotes character, it adds a second backslash. The resulting double-backslash causes the escaping to be effectively canceled out. This means that the double-quotes are processed unescaped, which closes the string that should contain the search term.

An arithmetic operator (in this case the subtraction operator) is then used to separate the expressions before the `alert()` function is called. Finally, a closing curly bracket and two forward slashes close the JSON object early and comment out what would have been the rest of the object. As a result, the response is generated as follows:

```
{"searchTerm":"\\"-alert(1)}//", "results":[]}
```

<figure><img src="../../../.gitbook/assets/image (821).png" alt=""><figcaption></figcaption></figure>
