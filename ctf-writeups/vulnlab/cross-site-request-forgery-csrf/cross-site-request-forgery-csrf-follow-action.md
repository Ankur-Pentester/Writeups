# Cross-Site Request Forgery (CSRF) – Follow Action

### Vulnerability : Cross-Site Request Forgery (CSRF) – Follow Action

#### 1. Vulnerability Name & Description

**Challenge Name:** Follow

**Cross-Site Request Forgery (CSRF)**

The follow functionality does not implement any CSRF protection. The application performs a state-changing action (following a user) using an HTTP GET request without validating a CSRF token. An attacker can craft a malicious link that forces an authenticated user to follow another account without their consent.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

[`http://localhost:1337/lab/csrf/follow/index.php`](http://localhost:1337/lab/csrf/follow/index.php)

**Authenticated User:**

user

**PoC URL Payload:**

```
<http://localhost:1337/lab/csrf/follow/index.php?follow=follow>
```

**Steps:**

1. Login as a valid user and maintain an active session.
2. Click the **Follow** button and observe the request in the browser network tab.
3. Notice that the follow action is executed using a GET request with the parameter `follow=follow`.
4. Copy and replay the URL directly or embed it in a malicious webpage or chat message.
5. The follow action is performed automatically without user confirmation.
6. The target account is added to the followers list successfully.

***

#### 3. Impact Analysis

* Unauthorized follow actions without user knowledge
* Abuse of social features and trust manipulation
* Forced interactions via phishing links
* Loss of user control over account activity
* Can be chained with other CSRF vulnerabilities

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 6.5 (Medium)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:U/C:N/I:L/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** MEDIUM

Although no direct account takeover occurs, the vulnerability allows attackers to manipulate user actions without consent, violating application integrity.

***

#### 6. Remediation / Mitigation Steps

1. Implement CSRF tokens for all state-changing requests
2. Change follow actions from GET to POST requests
3. Validate CSRF tokens server-side
4. Enforce SameSite cookie attributes
5. Require explicit user interaction for social actions

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (301).png" alt=""><figcaption></figcaption></figure>

* Follow button triggering the request
* Network request showing GET-based follow action

<figure><img src="../../../.gitbook/assets/image (302).png" alt=""><figcaption></figcaption></figure>

* Crafted URL executing follow automatically
* Followers list updated without user interaction

