# Bad Feedback ( Writeup )

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### I captured the request from the Feedback portal using Burp Suite’s Proxy module and forwarded it to the Repeater for detailed manual testing.

![image.png](<../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png>)

**To validate the presence of an XML External Entity (XXE) vulnerability in the Feedback endpoint, I modified the XML payload by inserting a custom `<!DOCTYPE>` declaration containing an external entity referencing a local file. The injected payload was as follows:**

```xml
<!DOCTYPE replace [
    <!ENTITY ent SYSTEM "file:///etc/shadow">
]>
```

**then invoked the external entity using `&ent;` within the `<name>` field. Once the request was intercepted in Burp Suite, it was forwarded to the Repeater for controlled testing. Upon execution, the server parsed the malicious XML and returned the contents of `/etc/shadow` in its response, confirming a successful XXE exploitation.**

![image.png](<../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png>)

**After successfully confirming the XXE vulnerability by reading `/etc/shadow`, I proceeded to test whether arbitrary files within the application’s root directory could also be accessed. To validate this, I created a malicious XML payload that defined an external entity referencing the `flag.txt` file located in the server’s root directory. The payload used was:**

```xml
<!DOCTYPE replace [
    <!ENTITY ent SYSTEM "file:///flag.txt">
]>
```

**By invoking the entity using `&ent;` within the `<name>` field and forwarding the modified request through Burp Suite Repeater, the server processed the external entity and returned the contents of `flag.txt` in the HTTP response. This confirmed that the vulnerability allowed unrestricted file disclosure on the host system, leading to exposure of sensitive application data (including the challenge flag)**

![image.png](<../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png>)

```jsx
Flag:- ClOuDsEk_ReSeArCH_tEaM_CTF_2025{b3e0b6d2f1c1a2b4d5e6f71829384756}
```
