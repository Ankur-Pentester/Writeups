# Insecure Deserialization (Authentication Bypass)

#### 1. Vulnerability Name & Description

**Challenge Name:** Admin Account – Insecure Deserialization

The application relies on a **Base64-encoded serialized PHP object stored inside a client-side cookie** to manage user authentication.

This serialized object contains **sensitive authentication data such as username and password**, which is **fully trusted by the server without integrity validation**.

Because the cookie is:

* Client-controlled
* Not signed or encrypted
* Directly deserialized on the server

An attacker can **decode, modify, and re-encode the serialized object**, resulting in **authentication bypass and privilege escalation to admin**.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/insecure-deserialization/admin-account-1/index.php>
```

**Authentication Mechanism:**

Client-side serialized object stored in cookie

**Cookie Name:**

```
V2VsY29tZ...
```

***

#### Steps to Reproduce

1. **Login as a normal user**
   * Username: `test`
   * Password: `test`
2. After successful login, open **Browser Developer Tools**
   * Navigate to **Storage → Cookies**
   * Copy the authentication cookie value
3. **Decode the cookie using CyberChef**
   * Apply:
     * `From Base64`
   * Decoded output reveals a serialized PHP object:

*
  * `O:4:"User":2:{ s:8:"username";s:4:"test"; s:8:"password";s:4:"test"; }`
*   **Modify the serialized object**

    * Change username from `test` to `admin`
    * Keep password unchanged

    Modified object:

1. `O:4:"User":2:{ s:8:"username";s:5:"admin"; s:8:"password";s:4:"test"; }`
2. **Re-encode the modified object**
   * Apply:
     * `To Base64`
3. **Replace the original cookie value** with the modified Base64 string
4. **Refresh the page**

***

#### 3. Result

The application authenticates the attacker as **admin without validating credentials**.

**Observed Output:**

```
Welcome admin!
```

***

#### 4. Impact Analysis

* Authentication bypass
* Unauthorized admin access
* Privilege escalation
* Complete compromise of access control
* Potential data exposure and manipulation
* Trust boundary violation (client → server)

***

#### 5. CVSS 3.1 Score & Vector String

**CVSS Base Score:** **8.8 (High)**

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H
```

***

#### 6. Risk Rating / Severity

**Severity Level:** **HIGH**

This vulnerability allows attackers to directly manipulate authentication data by tampering with client-side serialized objects, leading to full administrative access.

***

#### 7. Remediation / Mitigation Steps

1. Do not store authentication data in client-side cookies
2. Avoid PHP object serialization for session management
3. Use server-side sessions with secure session IDs
4. If serialization is unavoidable:
   * Implement cryptographic signing (HMAC)
   * Validate object integrity before deserialization
5. Never trust client-controlled data
6. Regenerate sessions after authentication
7. Set cookies with:
   * `HttpOnly`
   * `Secure`
   * `SameSite=Strict`

***

#### 8. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

* Normal user login (test)

<figure><img src="../../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

* Authentication cookie extracted from browser

<figure><img src="../../../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

* Base64 decoded serialized object
* Modified serialized payload `(username=admin)`

<figure><img src="../../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

* Re-encoded cookie value

<figure><img src="../../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

* Successful admin authentication (Welcome admin!)
