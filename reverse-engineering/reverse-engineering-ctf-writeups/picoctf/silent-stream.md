# Silent Stream

<figure><img src="../../../.gitbook/assets/image (595).png" alt=""><figcaption></figcaption></figure>

***

Encrypt Python Script <br>

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

Test

```
tshark -r packets.pcap -T fields -e data | tr -d '\n' > hex.txt
```

<figure><img src="../../../.gitbook/assets/image (596).png" alt=""><figcaption></figcaption></figure>

Test

Test

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

Test

Test

<figure><img src="../../../.gitbook/assets/image (597).png" alt=""><figcaption></figcaption></figure>

Test

Test

```
xdg-open flag.bin
```

<figure><img src="../../../.gitbook/assets/image (598).png" alt=""><figcaption></figcaption></figure>

#### Flag

```
picoCTF{tr4ck_th3_tr4ff1c_c1f1c1d9}
```
