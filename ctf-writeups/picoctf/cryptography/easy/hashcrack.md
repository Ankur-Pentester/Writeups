# hashcrack

#### Challenge :&#x20;

<figure><img src="../../../../.gitbook/assets/image (390).png" alt=""><figcaption></figcaption></figure>

Let's Start !!

Connect via a `Netcat`  !!

We Have a `hash` try to crack !!

<figure><img src="../../../../.gitbook/assets/image (391).png" alt=""><figcaption></figcaption></figure>

We use the `hashid` tool to identify the hash algorithm. After determining the hash type, we use John the Ripper to crack the hash. The `-j` flag is used to obtain the corresponding John the Ripper format for the identified hash.

<figure><img src="../../../../.gitbook/assets/image (392).png" alt=""><figcaption></figcaption></figure>

```
echo "<hash>" > hash
```

We use `echo` command above to Make the new file called hash !!

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 hash
```

The given hash was saved to a file and identified as an **MD5 hash**. Using **John the Ripper** with the `rockyou.txt` wordlist and the `raw-md5` format, a dictionary attack was performed. The hash was cracked successfully, revealing the plaintext password as **`password123`**.

<figure><img src="../../../../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>

We Get another Hash Let's Use Same step before and crack it again !!

<figure><img src="../../../../.gitbook/assets/image (394).png" alt=""><figcaption></figcaption></figure>

We use the `hashid` tool to identify the hash algorithm. After determining the hash type, we use John the Ripper to crack the hash. The `-j` flag is used to obtain the corresponding John the Ripper format for the identified hash.

<figure><img src="../../../../.gitbook/assets/image (395).png" alt=""><figcaption></figcaption></figure>

```
echo "<hash>" > hash
```

We use `echo` command above to Make the new file called hash !!

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha1 hash
```

The given hash was saved to a file and identified as an **SHA1 hash**. Using **John the Ripper** with the `rockyou.txt` wordlist and the `raw-sha1` format, a dictionary attack was performed. The hash was cracked successfully, revealing the plaintext password as **`letmein`**.

<figure><img src="../../../../.gitbook/assets/image (396).png" alt=""><figcaption></figcaption></figure>

We Get another Hash Let's Use Same step before and crack it again !!

<figure><img src="../../../../.gitbook/assets/image (397).png" alt=""><figcaption></figcaption></figure>

We use the `hashid` tool to identify the hash algorithm. After determining the hash type, we use John the Ripper to crack the hash. The `-j` flag is used to obtain the corresponding John the Ripper format for the identified hash.

```
echo "<hash>" > hash
```

We use `echo` command above to Make the new file called hash !!

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha256 hash
```

The given hash was saved to a file and identified as an **SHA256 hash**. Using **John the Ripper** with the `rockyou.txt` wordlist and the `raw-sha256` format, a dictionary attack was performed. The hash was cracked successfully, revealing the plaintext password as **`qwerty098`**.

<figure><img src="../../../../.gitbook/assets/image (398).png" alt=""><figcaption></figcaption></figure>

Boom !!!\
Finally We got a Flag !!

<figure><img src="../../../../.gitbook/assets/image (399).png" alt=""><figcaption></figcaption></figure>

```
Flag: picoCTF{UseStr0nG_h@shEs_&PaSswDs!_5b836723}
```
