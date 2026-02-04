# What is Web Cache Deception

### 1️⃣ What is Web Cache Deception (WCD)?

> Web Cache Deception is a vulnerability where a **web cache mistakenly stores sensitive dynamic content** by treating it as a **static resource**, due to **URL parsing differences** between the cache and origin server.

| Web Cache Deception         | Web Cache Poisoning             |
| --------------------------- | ------------------------------- |
| Leaks **real private data** | Injects **malicious fake data** |
| Exploits **cache rules**    | Exploits **cache keys**         |
| Attacker reads victim data  | Victims receive attacker data   |

### 3️⃣ Core Root Cause

> Cache and origin server **interpret the same URL differently**.

* Cache → checks **file extension / directory**
* Origin → processes **actual endpoint logic**

### 4️⃣ When is WCD possible?

All conditions must be true:

* Endpoint returns **sensitive dynamic data**
* Uses **GET / HEAD / OPTIONS**
* Cache has **static-based rules**
* Cache & origin parse URL **inconsistently**

### 5️⃣ Common Cache Rules

* **Static extension rules**\
  `.css`, `.js`, `.ico`, `.png`
* **Static directory rules**\
  `/static`, `/assets`, `/images`
* **File name rules**\
  `robots.txt`, `favicon.ico`

### 6️⃣ General Attack Flow

1. Find dynamic endpoint\
   `/profile`, `/account`
2. Confirm sensitive data in response
3. Identify cache parsing behavior
4. Craft URL triggering cache rule
5. Victim visits URL → response cached
6. Attacker requests same URL → data leaked

### 7️⃣ Path Mapping Discrepancy

**Idea:**\
Origin ignores extra path segments, cache does not.

**Example Payload:**

```
/user/123/profile/wcd.css
```

* Origin → `/user/123/profile`
* Cache → `.css` → cache response

### 8️⃣ Delimiter Discrepancy

{% file src="../../../.gitbook/assets/delimeter_bypass_list.txt" %}

**Idea:**\
Origin treats certain characters as delimiters, cache doesn’t.

**Common Delimiters:**

```
;  .  ?  %00  %23
```

**Example:**

```
/profile;test.css
```

* Origin → `/profile`
* Cache → `.css` → cached

### 9️⃣ Encoded Delimiter Discrepancy

**Idea:**\
Origin decodes encoded delimiter, cache does not.

**Example:**

```
/profile%23wcd.css   (%23 = #)
```

* Origin → `/profile`
* Cache → `.css` → cached

### 🔟 Static Directory + Path Traversal

**Idea:**\
Cache matches static prefix, origin normalizes path.

**Example:**

```
/assets/..%2fprofile
```

* Origin → `/profile`
* Cache → `/assets/*` → cached

### 1️⃣1️⃣ Normalization Discrepancy

**Normalization includes:**

* Decoding `%2f`, `%2e`
* Resolving `../`

**Exploit Structure:**

`/<static-prefix>/..%2f<dynamic-path>`

### 1️⃣2️⃣ Detecting Cached Responses

#### Headers:

```
X-Cache: hit
Cache-Control: public
Age: > 0
```

#### Timing:

* First request → slow
* Second request → fast

### 1️⃣3️⃣ Testing Payload Cheat Sheet

```
/profile.css
/profile;.js
/profile%00test.css
/profile%23abc.css
/assets/..%2fprofile
/profile%2f%2e%2e%2fstatic
```

Always use cache buster:

```
?cb=123
```

### 1️⃣4️⃣ Key Difference to Remember

> **WCD abuses cache RULES, not cache KEYS**

### 1️⃣5️⃣ One-Line Memory Hook 🧠

> **“If cache thinks static and origin serves dynamic → Web Cache Deception.”**

### 1️⃣6️⃣ Prevention (Defensive View)

* Use:

```
Cache-Control: no-store, private
```

* Validate **Content-Type vs file extension**
* Avoid ambiguous URL parsing
* Enable CDN protections (e.g. Cache Deception Armor)

### 🔥 Final Revision Formula (EXAM / CTF)

`Dynamic endpoint + Static-looking URL + Parsing mismatch = Web Cache Deception`









