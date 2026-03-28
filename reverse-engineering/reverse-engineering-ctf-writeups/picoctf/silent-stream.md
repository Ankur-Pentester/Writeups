# Silent Stream

<figure><img src="../../../.gitbook/assets/image (595).png" alt=""><figcaption></figcaption></figure>

***

Given Encrypt Python Script <br>

```
import socket

def encode_byte(b, key):

    return (b + key) % 256

def simulate_flag_transfer(filename, key=42):
    print(f"[!] flag transfer for '{filename}' using encoding key = {key}")

    with open(filename, "rb") as f:
        data = f.read()

    print(f"[+] Encoding and sending {len(data)} bytes...")

    for b in data:
        encoded = encode_byte(b, key)
        pass

    print("Transfer complete")

if __name__ == "__main__":
    simulate_flag_transfer("flag.txt")
```

We Have a `.pcap`  packets file let's grep the all raw  data in `hex.txt` file using tshark and Decrypt The all data using below python script !

```
tshark -r packets.pcap -T fields -e data | tr -d '\n' > hex.txt
```

<figure><img src="../../../.gitbook/assets/image (596).png" alt=""><figcaption></figcaption></figure>

We Use `Decrypt.py` python Script To decrypt the encrypt raw data !!

```
# Use The tshark Raw Data File 
with open("hex.txt") as f:
    hex_data = f.read().strip()

data = bytes.fromhex(hex_data)

decoded = bytes([(b - 42) % 256 for b in data])

with open("flag.bin", "wb") as f:
    f.write(decoded)

print("Done!")
```

Then We Use `File` Command To Check The File Details !!

Below The Image flag.bin file is a `jpeg` image data file .

<figure><img src="../../../.gitbook/assets/image (597).png" alt=""><figcaption></figcaption></figure>

Then We Use Below Command to open the image !!

```
xdg-open flag.bin
```

<figure><img src="../../../.gitbook/assets/image (598).png" alt=""><figcaption></figcaption></figure>

#### Flag

```
picoCTF{tr4ck_th3_tr4ff1c_c1f1c1d9}
```
