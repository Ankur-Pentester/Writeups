# Bella Ciao Secure Comms

The Professor's communication server has been recovered after a failed operation.

The crew believed their messages were secure, but intelligence reports suggest a flaw exists somewhere inside the system. Berlin, Tokyo, Nairobi, and Denver all used this platform to exchange sensitive information during the heist.

Your objective is simple: gain access, investigate the application, and uncover the hidden secret before the authorities do.

Flag Format: ZeroDayHeist{...}

Bella Ciao, operatives.

***

First Visit The Website Properly and Think How It's Work !!

<figure><img src="../../../.gitbook/assets/image (839).png" alt=""><figcaption></figcaption></figure>

Then We Redirect On The Login page and I Login using This credential : `denver/hahahahaha`

<figure><img src="../../../.gitbook/assets/image (840).png" alt=""><figcaption></figcaption></figure>

In Website  we see this website is powered by jinja2 <--— This is a red flag !!



Then I use The SSTI Payloads and It's Work Then here i got a Secret\_key= xxxx-xxxx-xxxx



After Thinking I Think Website use Flask  Library Cookie !!

<figure><img src="../../../.gitbook/assets/image (841).png" alt=""><figcaption></figcaption></figure>

Here Is The flask cookie decode looks like This !!

```
base64(payload).timestamp.signature
```

```
## Decode 
{"codename":"Denver","role":"recruit","user":"denver"}
```

Then i use `flask-unsign` python library and Tamper the cookie and I Change The Role To elite !!

```
flask-unsign --sign --cookie '{"codename":"Denver","role":"elite","user":"denver"}' --secret 'lacasadepapel-secret-2024'
```

We Forged The cookie With The High privilege Role !!

<figure><img src="../../../.gitbook/assets/image (842).png" alt=""><figcaption></figcaption></figure>

Then Paste The Cookie In Storage !!

<figure><img src="../../../.gitbook/assets/image (843).png" alt=""><figcaption></figcaption></figure>

Then i able To Visit The INTEL ROOM and I Got The Flag !!

<figure><img src="../../../.gitbook/assets/image (844).png" alt=""><figcaption></figcaption></figure>

```
Flag : Zer0d4yh31st{sst1_t0_rce_b3ll4_c14o}
```

