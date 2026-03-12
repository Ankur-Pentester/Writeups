# Transformation

<figure><img src="../../../.gitbook/assets/image (581).png" alt=""><figcaption></figcaption></figure>

***

Let's First Download The `enc` File !!

<figure><img src="../../../.gitbook/assets/image (582).png" alt=""><figcaption></figcaption></figure>

```
灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸形㝦㘲捡㕽
```

The challenge provides the following Python code snippet:

```
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```

The goal is to understand how the flag is transformed and then reverse the process to recover the original flag.

## Understanding the Encoding

The provided code performs the following steps:

#### 1️⃣ Convert characters to ASCII

```
ord(flag[i])
```

This converts a character into its ASCII value.

Example:

```
'A' → 65
```

***

#### 2️⃣ Bitwise Left Shift

```
ord(flag[i]) << 8
```

This shifts the bits **8 positions to the left**, effectively multiplying the value by **256**.

Example:

```
65 << 8 = 16640
```

***

#### 3️⃣ Combine Two Characters

```
(ord(flag[i]) << 8) + ord(flag[i + 1])
```

This packs **two characters into one 16-bit value**.

Example:

```
'A' = 65
'B' = 66

encoded = (65 << 8) + 66
        = 16706
```

***

#### 4️⃣ Convert Back to Character

```
chr(encoded)
```

The combined value becomes a **Unicode character**.

So the encoding process is:

```
2 characters → 1 Unicode character
```

## Reversing the Transformation

To recover the original characters, we reverse the process.

If:

```
encoded = (char1 << 8) + char2
```

Then:

```
char1 = encoded >> 8
char2 = encoded & 255
```

Explanation:

* `>> 8` extracts the first byte
* `& 255` extracts the second byte

***

## Decoder Script

The encoded characters were stored in a file named `enc`.\
We wrote the following Python script to decode them.

```
a = open('enc', 'r').read()

flag = ""

for c in a:
    num = ord(c)
    flag += chr(num >> 8)
    flag += chr(num & 255)

print(flag)
```

Run The Script In Terminal !!<br>

<figure><img src="../../../.gitbook/assets/image (584).png" alt=""><figcaption></figcaption></figure>

```
picoCTF{16_bits_inst34d_of_8_b7f62ca5}
```

