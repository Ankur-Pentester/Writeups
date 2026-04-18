# What is Web cache poisoning ?

Web cache poisoning is an attack where an attacker manipulates a website’s caching system so that a malicious HTTP response is stored in the cache and then served to other users.

Instead of attacking users individually, the attacker poisons the cache once and lets the system distribute the attack automatically.

***

### How Web Caching Works

A web cache sits between the user and the server:

```
User → Cache → Server
```

#### Normal Behavior:

1. First request → goes to server
2. Server responds
3. Cache stores the response
4. Future requests → served directly from cache

Result: Faster performance and reduced server load

***

### What is a Cache Key?

A cache uses a **cache key** to decide whether two requests are the same.

#### Common cache key components:

* URL (path + query)
* Host header

#### Example:

```
GET /home
Host: example.com
```

Cache key:

```
example.com + /home
```

***

### What are Unkeyed Inputs?

Unkeyed inputs are parts of a request **not included in the cache key**.

#### Examples:

* `X-Forwarded-Host`
* `User-Agent`
* Cookies (sometimes)

Cache ignores them\
But server may still use them

This mismatch leads to vulnerabilities

***

### How Web Cache Poisoning Works

#### Step 1: Identify Unkeyed Input

Send modified requests and check if the response changes:

```
X-Forwarded-Host: attacker.com
```

If the response reflects this → potential entry point

***

#### Step 2: Generate Malicious Response

Inject payload:

```
X-Forwarded-Host: a."><script>alert(1)</script>
```

Response:

```
<script>alert(1)</script>
```

***

#### Step 3: Get It Cached

If the response is cached:

```
X-Cache: HIT
```

The poisoned response is now stored

***

#### Step 4: Victims Receive It

Normal users request the same page:

```
GET /home
```

They receive the malicious cached response

Attack complete

***

### Safe Testing (Cache Buster)

To avoid affecting real users:

Add a unique parameter:

```
GET /home?cb=12345
```

Each request becomes unique → only you see the result

***

### Types of Attacks Using Cache Poisoning

***

#### 1. Cross-Site Scripting (XSS)

**Example:**

```
X-Forwarded-Host: a."><script>alert(1)</script>
```

Response:

```
<script>alert(1)</script>
```

**Impact:**

* Session hijacking
* Account takeover
* Data theft

***

#### 2. Malicious JavaScript Injection

**Example:**

```
X-Forwarded-Host: evil.com
```

Response:

```
<script src="https://evil.com/script.js"></script>
```

**Impact:**

* Keylogging
* Credential theft
* Full control over user sessions

***

#### 3. Open Redirect

**Example:**

```
Location: https://evil.com
```

**Impact:**

* Phishing attacks
* User redirection to malicious sites

***

#### 4. Cookie-based Cache Poisoning

**Scenario:**

```
Cookie: language=pl
```

Cache ignores cookies → stores Polish version

**Impact:**

* Wrong content delivery
* Possible XSS if cookies are reflected

***

#### 5. Content Manipulation / Defacement

**Example:**

* Inject fake messages
* Modify page content

**Impact:**

* Brand damage
* Misinformation

***

### Impact of Web Cache Poisoning

Impact depends on:

#### 1. Payload severity

* Simple HTML → low impact
* XSS / JS → high impact

#### 2. Traffic of the page

* Low traffic → limited impact
* Homepage → massive impact

One poisoned response can affect thousands of users

***

### Why This Happens

Root cause:

* Cache ignores some inputs
* Server trusts those inputs

#### Key Idea:

**“If user input affects the response but not the cache key → vulnerability”**
