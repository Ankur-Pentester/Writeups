# VulnNet: Roasted

<figure><img src="../.gitbook/assets/image (901).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/vulnnetroasted" %}

***

First Let's use The nmap .

```
sudo nmap -sV -sC -Pn 10.48.133.159 -T4
```

<figure><img src="../.gitbook/assets/image (902).png" alt=""><figcaption></figcaption></figure>

List The SMB services .

```
smbclient -L //10.48.133.159/ -N
```

<figure><img src="../.gitbook/assets/image (903).png" alt=""><figcaption></figcaption></figure>

TEST

```
smbclient //10.48.133.159/VulnNet-Business-Anonymous -N
```

<figure><img src="../.gitbook/assets/image (904).png" alt=""><figcaption></figcaption></figure>

Same For The Second Share .

```
smbclient //10.48.133.159/VulnNet-Enterprise-Anonymous -N
```

<figure><img src="../.gitbook/assets/image (905).png" alt=""><figcaption></figcaption></figure>

TEST
