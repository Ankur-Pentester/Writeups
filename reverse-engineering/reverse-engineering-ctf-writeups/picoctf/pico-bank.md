# Pico Bank

<figure><img src="../../../.gitbook/assets/image (588).png" alt=""><figcaption></figcaption></figure>

***

Let's First Visit The Pico Bank Website !!

Download The Pico Bank App !!

<figure><img src="../../../.gitbook/assets/image (589).png" alt=""><figcaption></figcaption></figure>

We Have a `pico-bank.apk` File.&#x20;

Let's decode The `.apk` File Using `apktool`.

```
apktool d pico-bank.apk
```

We Got The `pico-bank` Folder after Decode The `.apk` File .

<figure><img src="../../../.gitbook/assets/image (590).png" alt=""><figcaption></figcaption></figure>

Let's Use `jadx-gui` Tool For To analyze the `pico-bank.apk`  File Source Code .&#x20;

```
jadx-gui ~/ctf/pico-bank.apk
```

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

`OTP.java` File We Got interesting Endpoint  is `/verify-otp` .

Important code found:

```
if (getResources().getString(R.string.otp_value).equals(otp)) {
    Intent intent = new Intent(this, MainActivity.class);
    startActivity(intent);
    finish();
}
```

This shows that:

* The OTP is **hardcoded**
* It is stored in `strings.xml`

<figure><img src="../../../.gitbook/assets/image (591).png" alt=""><figcaption></figcaption></figure>

Then We Visit The Hidden Endpoint `/verify-otp`  They Return `GET` Method Is Not Allowed In This Endpoint .

<figure><img src="../../../.gitbook/assets/image (594).png" alt=""><figcaption></figcaption></figure>

Then We Use The `Curl` Command and Send The Post Request Then We Got The `JSON` Output We Got Invalid `OTP` Let's analyze The Source Code and Find The Some Valuable Information .

<figure><img src="../../../.gitbook/assets/image (592).png" alt=""><figcaption></figcaption></figure>

We Open The `apktool` Decompile `apk` File In `android-Studio` To Analyze The File .

We Finally Find The `Strings.xml` File .

`res/values/strings.xml`

```
<string name="otp_value">9673</string>
```

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Then We Use The Below `Curl` Command and Then We Got The First Part of The Flag !!

```
curl http://amiable-citadel.picoctf.net:61228/verify-otp -X POST -H "Content-Type: application/json" -d '{"otp":"9673"}'
```

<figure><img src="../../../.gitbook/assets/image (593).png" alt=""><figcaption></figcaption></figure>

### First Part of The Flag !!

```
s3cur3d_m0b1l3_l0g1n_1ff8ddb7} <--  First Part of The Flag
```

In `jadx-gui`  Source Code Then analyze The `MainActivity.java` File and We Got The Transaction List !!

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Inside the transaction list, we notice something suspicious:

> 1110000\
> 1101001\
> 1100011\
> 1101111\
> 1000011\
> 1010100\
> 1000110\
> 1111011\
> 110001\
> 1011111\
> 1101100\
> 110001\
> 110011\
> 1100100\
> 1011111\
> 110100\
> 1100010\
> 110000\
> 1110101\
> 1110100\
> 1011111\
> 1100010\
> 110011\
> 110001\
> 1101110\
> 1100111\
> 1011111

**Binary → Decimal Conversion**

Convert all binary number to decimal

**Decimal → ASCII Characters**

Converting those decimals to ASCII gives:

### Second Part of the Flag <a href="#id-2262" id="id-2262"></a>

```
picoCTF{1_l13d_4b0ut_b31ng_
```

### Final Answer: <a href="#id-2220" id="id-2220"></a>

```
picoCTF{1_l13d_4b0ut_b31ng_s3cur3d_m0b1l3_l0g1n_1ff8ddb7}
```



