# Nitro ( Writeup )

Given that the Nitro challenge was intentionally designed to be unsolvable through manual interaction, I implemented an automated Python solver. The script performed iterative requests to /task, parsed the dynamic string, applied the prescribed transformations (reverse → Base64 encode → CSK wrapper), and submitted the result to /submit within milliseconds. This automation successfully bypassed the challenge’s time-based restriction and returned the final flag

```
import requests, re, base64

BASE = "http://15.206.47.5:9090"
session = requests.Session()

while True:
    # 1. GET /task
    r = session.get(BASE + "/task")
    text = r.text

    m = re.search(r"input string:\s*([^<]+)", text, re.I)
    if not m:
        print("No input found:", text)
        continue

    s = m.group(1).strip()

    # 2. Reverse, Base64 encode, wrap
    payload = "CSK__" + base64.b64encode(s[::-1].encode()).decode() + "__2025"

    # 3. POST /submit
    p = session.post(BASE + "/submit", data=payload,
                     headers={"Content-Type": "text/plain"})

    print("Input:", s, "| Payload:", payload)
    print("Reply:", p.text.strip())

    if "flag" in p.text.lower():
        print("\nFLAG FOUND!")
        print(p.text)
        break

```

## **1. Importing Required Modules**

```python
import requests, re, base64
```

* **requests** → For making HTTP GET/POST requests to the challenge server.
* **re** → To extract the input string from the HTML response using regex.
* **base64** → For encoding the reversed string as required by the challenge.

***

## **2. Setting Base URL and Initializing a Session**

```python
BASE = "<http://15.206.47.5:9090>"
session = requests.Session()
```

* The base URL of the Nitro challenge is stored in `BASE`.
* A **session** object is created to reuse the same connection, improving speed and avoiding overhead—important for a timed challenge.

***

## **3. Continuous Loop for Fast Automation**

```python
while True:
```

Nitro requires rapid repeated submissions.

A loop ensures:

* Fetch input
* Process input
* Submit payload
* Repeat until flag appears

***

## **4. Fetching the Task**

```python
r = session.get(BASE + "/task")
text = r.text
```

This sends a **GET request** to `/task`, which returns an HTML page containing something like:

```
Here is the input string: hsjWQ9I65U1k
```

The content is stored in `text`.

***

## **5. Extracting the Input String Using Regex**

```python
m = re.search(r"input string:\\s*([^<]+)", text, re.I)
if not m:
    print("No input found:", text)
    continue

s = m.group(1).strip()
```

* Regex locates the line containing “input string:”
* `( [^<]+ )` captures everything until the next HTML tag
* `.group(1)` extracts the actual string
* `.strip()` removes spaces/newlines

Example extracted value:

```
hsjWQ9I65U1k
```

***

## **6. Transforming the Input (Reverse → Base64 → Wrap)**

```python
payload = "CSK__" + base64.b64encode(s[::-1].encode()).decode() + "__2025"
```

The transformation required by the challenge:

#### 1️⃣ **Reverse the string**

`s[::-1]`

Example:

`hsjWQ9I65U1k` → `k1U56I9QWjsh`

#### 2️⃣ **Base64 encode the reversed string**

`base64.b64encode(...).decode()`

Example:

`k1U56I9QWjsh` → `azFVNTZJOVFXanNo`

#### 3️⃣ **Wrap in CSK format**

```
CSK__<b64 payload>__2025
```

Final payload example:

```
CSK__azFVNTZJOVFXanNo__2025
```

***

## **7. Submitting the Payload to the Server**

```python
p = session.post(BASE + "/submit", data=payload,
                 headers={"Content-Type": "text/plain"})
```

* The challenge expects raw text body
* Content-Type explicitly set to `text/plain`
* The transformed payload is sent to `/submit`

***

## **8. Printing Progress**

```python
print("Input:", s, "| Payload:", payload)
print("Reply:", p.text.strip())
```

This helps track each iteration.

***

## **9. Detecting the Flag**

```python
if "flag" in p.text.lower():
    print("\\nFLAG FOUND!")
    print(p.text)
    break
```

* Converts response to lowercase
* Looks for keyword `"flag"`
* If found → stops the loop and prints final flag

### Final Run The Script

<figure><img src="../../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

```
Flag : ClOuDsEk_ReSeArCH_tEaM_CTF_2025{ab03730caf95ef90a440629bf12228d4}
```
