# Cross-Site Request Forgery (CSRF) – Money Transfer

### Vulnerability : Cross-Site Request Forgery (CSRF) – Money Transfer

#### 1. Vulnerability Name & Description

**Challenge Name:** Money Transfer

**Cross-Site Request Forgery (CSRF)**

The money transfer functionality performs sensitive state-changing operations using HTTP GET requests without implementing any CSRF protection. An attacker can craft a malicious link that, when visited by an authenticated user, automatically triggers an unauthorized money transfer without the user’s consent or interaction.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

`http://localhost:1337/lab/csrf/money-transfer/index.php`

**Authenticated User:**

user

**PoC URL Payload:**

```
<http://localhost:1337/lab/csrf/money-transfer/index.php?transfer_amount=1000&receiver=user>
```

**Steps:**

1. Login as a valid user with an active session.
2. Intercept the money transfer request using the browser network tab.
3. Observe that the transfer is executed via a GET request with parameters `transfer_amount` and `receiver`.
4. Replay or directly visit the crafted URL.
5. The money transfer is completed successfully without any CSRF token validation.
6. The account balance changes automatically, confirming unauthorized transaction execution.

***

#### 3. Impact Analysis

* Unauthorized money transfers without user approval
* Complete financial manipulation of victim accounts
* Abuse via phishing emails or malicious websites
* No user interaction required beyond being logged in
* High risk of financial fraud and account compromise

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 8.8 (High)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:U/C:N/I:H/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** HIGH

This vulnerability allows attackers to perform critical financial operations on behalf of authenticated users, leading to direct monetary loss.

***

#### 6. Remediation / Mitigation Steps

1. Implement CSRF tokens for all state-changing requests
2. Change sensitive actions from GET to POST requests
3. Validate CSRF tokens server-side for every transaction
4. Enforce SameSite cookie attributes (`SameSite=Strict`)
5. Add confirmation steps for financial actions
6. Implement transaction logging and anomaly detection

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (297).png" alt=""><figcaption></figcaption></figure>

* Money transfer form submission

<figure><img src="../../../.gitbook/assets/image (298).png" alt=""><figcaption></figcaption></figure>

* Account balance change confirmation

<figure><img src="../../../.gitbook/assets/image (299).png" alt=""><figcaption></figcaption></figure>

* Network request showing GET-based transfer

<figure><img src="../../../.gitbook/assets/image (300).png" alt=""><figcaption></figcaption></figure>

* Successful transfer without CSRF token
* Replayed malicious URL executing transfer

