# Insecure Deserialization (Authentication Bypass)

#### 1. Vulnerability Name & Description

**Challenge Name:** Admin Account v2 – Insecure Deserialization

The application uses a **Base64-encoded serialized PHP object** stored inside a client-side cookie to maintain authentication state.

This serialized object contains **sensitive fields such as username, password hash, and admin role flag**.

Because the cookie is:

* Not cryptographically signed
* Not integrity-protected
* Fully trusted by the server

An attacker can **decode, modify, and re-encode the serialized object**, allowing **privilege escalation from a normal user to admin** without knowing valid admin credentials.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/insecure-deserialization/admin-account-2/index.php>
```

**Authentication Method:**

Client-side serialized object stored in a cookie

**Cookie Name:**

```
d2VsY2...
```

***

#### Steps to Reproduce

1. **Login as a normal user**
   * Username: `test`
   * Password: `test`
2. After successful login, open **Browser Developer Tools**
   * Navigate to **Storage → Cookies**
   * Copy the authentication cookie value
3. **Decode the cookie**
   * Use CyberChef
   * Apply:
     * `From Base64`
     * `URL Decode`
4. The decoded value reveals a **serialized PHP object**, for example:

* `O:4:"User":3:{ s:8:"username";s:32:"098f6bcd4621d373cade4e832627b4f6"; s:8:"password";s:32:"098f6bcd4621d373cade4e832627b4f6"; s:7:"isAdmin";i:0; }`
* **Crack or identify hashes**
  * The MD5 hash `098f6bcd4621d373cade4e832627b4f6` resolves to:
*
  * `test`
* **Modify the serialized object**
  * Change the username hash to **MD5("administrator")**
  * Change admin flag:
* `isAdmin = 1`

Modified object:

1. `O:4:"User":3:{ s:8:"username";s:32:"200ceb26807d6bf99fd6f4fd01ca54d4"; s:8:"password";s:32:"098f6bcd4621d373cade4e832627b4f6"; s:7:"isAdmin";i:1; }`
2. **Re-encode the object**
   * Apply:
     * `To Base64`
     * `URL Encode`
3. Replace the original cookie value with the **modified encoded value**
4. **Refresh the page**

***

#### 3. Result

The application now authenticates the attacker as an **administrator**.

**Observed Output:**

```
Welcome Admin
```

No admin credentials were required.

***

#### 4. Impact Analysis

* Authentication bypass
* Privilege escalation to administrator
* Full account takeover
* Unauthorized access to admin-only functionality
* Potential data manipulation or deletion
* Complete compromise of application trust model

***

#### 5. CVSS 3.1 Score & Vector String

**CVSS Base Score:** **9.1 (Critical)**

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H
```

***

#### 6. Risk Rating / Severity

**Severity Level:** **CRITICAL**

This vulnerability allows attackers to fully control authentication and authorization logic by modifying client-side data, leading to total system compromise.

***

#### 7. Remediation / Mitigation Steps

1. **Never trust client-side serialized objects**
2. Do not store authentication state in cookies
3. Use server-side session management only
4. If serialization is required:
   * Use cryptographic signing (HMAC)
   * Validate integrity before deserialization
5. Avoid PHP object serialization for security-sensitive data
6. Implement proper access control checks on the server
7. Regenerate session IDs after privilege changes
8. Set cookies with:
   * `HttpOnly`
   * `Secure`
   * `SameSite=Strict`

***

#### 8. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

* Login as normal user (test)

<figure><img src="../../../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

* Authentication cookie extracted from browser storage

<figure><img src="../../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

* Base64 + URL decoded serialized object

<figure><img src="../../../.gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>

* Hash cracking via CrackStation

<figure><img src="../../../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>

* Regenerate the hash of (Administrator)

<figure><img src="../../../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>

* Modified serialized payload `(isAdmin=1)`
* Re-encoded cookie value

<figure><img src="../../../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (227).png" alt=""><figcaption></figcaption></figure>

* Final admin access confirmation `(Welcome Admin)`

