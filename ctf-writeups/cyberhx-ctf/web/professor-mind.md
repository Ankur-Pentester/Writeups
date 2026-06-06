# Professor Mind

## 🎭 La Casa de Papel — The Digital Heist

> _"Soy un hombre que va solo, sin ataduras..."_

The Governor's most secure vault is now **online**.\
El Profesor has been planning this for **months**.\
The security team thinks they're **untouchable**.

**Prove them wrong. Get inside. Find the flag.**

🚩 **Flag Format:** `Zer0d4yh31st{...}`

_Bella Ciao, Bella Ciao, Bella Ciao Ciao Ciao..._ 🔴

***

First Check The Website all Functions !!<br>

<figure><img src="../../../.gitbook/assets/image (829).png" alt=""><figcaption></figcaption></figure>

Then I Logged in Using Hidden Credential `guest:guest123`

<figure><img src="../../../.gitbook/assets/image (830).png" alt=""><figcaption></figcaption></figure>

We have a Two Functions Check One by One !!

<figure><img src="../../../.gitbook/assets/image (831).png" alt=""><figcaption></figcaption></figure>

DEAD TRANSMISSIONS !!\
\
We Got Hint a Hidden Directory /internal-\[something]

<figure><img src="../../../.gitbook/assets/image (832).png" alt=""><figcaption></figcaption></figure>

In Here Is SSRF Possible Because off Some Given Hint in The Website.

<figure><img src="../../../.gitbook/assets/image (833).png" alt=""><figcaption></figcaption></figure>

Then i Use Localhost IP with /interal-api Directory !!

I Enumerate The Port Like 8000,5000,4000,3000 but 5000 port work !!\
\
I Got The First Part Off The Flag and Some Hint also for Next Part !!

<figure><img src="../../../.gitbook/assets/image (834).png" alt=""><figcaption></figcaption></figure>

Second Hint !!

<figure><img src="../../../.gitbook/assets/image (835).png" alt=""><figcaption></figcaption></figure>

We Got Second Part Of The flag and also got The 3rd part of The Flag Hint  !!

```
http://127.0.0.1:5000/internal-api/config 
```

<figure><img src="../../../.gitbook/assets/image (836).png" alt=""><figcaption></figcaption></figure>

3rd Hint and Its Mean /flag.txt We can read !!

<figure><img src="../../../.gitbook/assets/image (837).png" alt=""><figcaption></figcaption></figure>

Then I use The Token We Find In The Previous Part !!

<figure><img src="../../../.gitbook/assets/image (838).png" alt=""><figcaption></figcaption></figure>

FLAG : Zer0d4yh31st{d34d\_m3n\_t3ll\_n0\_t4l3s}
