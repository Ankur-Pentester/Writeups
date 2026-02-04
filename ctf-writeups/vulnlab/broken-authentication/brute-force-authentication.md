# Brute Force Authentication

#### 1. Vulnerability Name & Description

**Challenge Name:** Brute Force Attack

**Brute Force Authentication – Broken Authentication**

The login functionality does not implement any protection mechanisms such as account lockout, rate limiting, or CAPTCHA. This allows an attacker to repeatedly submit authentication requests with different passwords for a known username until valid credentials are discovered.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

[`http://localhost:1337/lab/broken-authentication/brute-force/`](http://localhost:1337/lab/broken-authentication/brute-force/)

**Target Username:**

`admin`

**Attack Method:**

Automated brute-force attack using Hydra with a common password wordlist.

**Command Used:**

```
hydra-l admin -P/usr/share/wordlists/10k-most-common.txt 127.0.0.1 -s 1337 http-post-form "/lab/broken-authentication/brute-force/:username=^USER^&password=^PASS^:Wrong Password"
```

**Steps:**

1. Identified the login form accepting username and password inputs (Figure 1).
2. Observed a consistent error message for invalid credentials, enabling response-based detection.
3. Executed a brute-force attack using a common credentials wordlist (Figure 2).
4.  Hydra successfully identified valid credentials:

    **Username:** admin

    **Password:** lifehack
5. Logged in successfully using the discovered credentials and received a success message (Figure 3).

***

#### 3. Impact Analysis

* Unauthorized access to user accounts
* Account takeover of privileged users (admin)
* Exposure of sensitive application data
* Potential privilege escalation
* Increased risk of further attacks such as data modification or deletion

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 8.2 (High)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:L/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** HIGH

The vulnerability allows full compromise of user accounts without any user interaction and can be exploited remotely using automated tools.

***

#### 6. Remediation / Mitigation Steps

1. Implement account lockout after multiple failed login attempts
2. Apply rate limiting on authentication endpoints
3. Introduce CAPTCHA after a defined number of failures
4. Enforce strong password policies
5. Log and monitor failed authentication attempts
6. Use multi-factor authentication (MFA) for privileged accounts

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (284).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Login page showing failed authentication message

<figure><img src="../../../.gitbook/assets/image (285).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Hydra brute-force attack successfully discovering valid credentials

<figure><img src="../../../.gitbook/assets/image (286).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Successful login confirmation message after brute-force attack
