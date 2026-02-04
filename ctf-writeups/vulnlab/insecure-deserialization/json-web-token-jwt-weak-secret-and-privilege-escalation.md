# JSON Web Token (JWT) Weak Secret & Privilege Escalation

### Vulnerability : JSON Web Token (JWT) Weak Secret & Privilege Escalation

***

#### 1. Vulnerability Name & Description

**Challenge Name:** JWT Attack – Insecure Deserialization

**Vulnerability Type:** Broken Authentication & Authorization (JWT Weak Secret)

The application uses JSON Web Tokens (JWT) for authentication but signs them using a **weak and guessable secret key**. Due to this misconfiguration, an attacker can brute-force the JWT secret, tamper with the token payload, and escalate privileges by forging a new valid token.

This results in unauthorized access to higher-privileged accounts without knowing valid credentials.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/insecure-deserialization/jwt-attack/index.php>
```

**Authentication Mechanism:**

JWT stored in browser cookies

***

#### Step 1: Login as Normal User

**Valid Credentials Used:**

```
Username: Yavuzlar
Password: Vulnlab
```

After successful login, the application redirects to the normal user page.

***

#### Step 2: Extract JWT from Browser Storage

Using browser Developer Tools:

* Open **Storage → Cookies**
* Extract the JWT token stored for `localhost`

This token is used by the application for authentication and authorization.

***

#### Step 3: Brute-Force JWT Secret Key

The extracted JWT is submitted to **JWT Auditor** using the **Secret Bruteforce** feature.

**Tool Used:** JWT Auditor

**Result:**

```
SecretKey Found: dragon
```

This confirms the JWT secret is weak and guessable.

***

#### Step 4: Modify JWT Payload (Privilege Escalation)

Using JWT Auditor **JWT Editor**:

* Original Payload:

```json
{
"username":"user"
}
```

* Modified Payload:

```json
{
"username":"admin"
}
```

* Algorithm: `HS256`
* Secret Key Used:

```
dragon
```

A new forged JWT is generated using the cracked secret key.

***

#### Step 5: Replace JWT in Browser

* Replace the existing JWT cookie value with the forged JWT
* Refresh the page

***

#### Step 6: Unauthorized Admin Access

The application accepts the forged JWT and grants **administrator-level access**, even though no valid admin credentials were provided.

***

#### 3. Impact Analysis

* Complete authentication bypass
* Privilege escalation from normal user to admin
* JWT integrity fully compromised
* Unauthorized access to sensitive admin functionality
* Potential data manipulation and account takeover
* Trust boundary failure in authentication system

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 8.8 (High)

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N
```

***

#### 5. Risk Rating / Severity

**Severity Level:** **HIGH**

The vulnerability allows attackers to forge valid authentication tokens and impersonate any user, including administrators.

***

#### 6. Remediation / Mitigation Steps

1. Use a **strong, long, randomly generated JWT secret**
2. Rotate JWT secrets periodically
3. Never hardcode secrets in source code
4. Use asymmetric signing (RS256) instead of symmetric (HS256)
5. Validate JWT claims strictly (issuer, audience, expiration)
6. Store JWT securely with `HttpOnly` and `Secure` flags
7. Implement server-side authorization checks (do not trust JWT role alone)

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (243).png" alt=""><figcaption></figcaption></figure>

* Login page with normal user credentials

<figure><img src="../../../.gitbook/assets/image (244).png" alt=""><figcaption></figcaption></figure>

* JWT token extracted from browser cookies

<figure><img src="../../../.gitbook/assets/image (245).png" alt=""><figcaption></figcaption></figure>

* JWT Auditor brute-force showing secret key `dragon`

<figure><img src="../../../.gitbook/assets/image (246).png" alt=""><figcaption></figcaption></figure>

* Modified JWT payload with admin role
* Generated forged JWT

<figure><img src="../../../.gitbook/assets/image (247).png" alt=""><figcaption></figcaption></figure>

* Successful privilege escalation after token replacement
