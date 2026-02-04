# CAPTCHA Bypass

#### 1. Vulnerability Name & Description

**Challenge Name:** Captcha-ByPass

**CAPTCHA Bypass – Insufficient Server-Side Validation**

The application implements CAPTCHA verification only at the client side. The server does not properly validate the CAPTCHA value before processing user input. As a result, an attacker can submit comments successfully even when the CAPTCHA is incorrect or reused, effectively bypassing the CAPTCHA protection mechanism.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

[`http://localhost:1337/lab/captcha-bypass/bypass1/index.php`](http://localhost:1337/lab/captcha-bypass/bypass1/index.php)

**Attack Method:**

Manual CAPTCHA reuse / logic bypass through normal form submission.

**Steps:**

1. Entered a comment value in the input field (Figure 1).
2. Provided any CAPTCHA value and submitted the form.
3. Observed that the application accepted the request and displayed a success message (Figure 2).
4. Repeated the process multiple times without solving new CAPTCHA challenges.
5. Comments were added successfully to the table despite ineffective CAPTCHA enforcement (Figure 3).

***

#### 3. Impact Analysis

* CAPTCHA protection rendered ineffective
* Automated spam and bot submissions possible
* Increased risk of brute-force and abuse attacks
* Bypass of anti-automation security controls
* Potential exploitation in combination with other vulnerabilities

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 6.5 (Medium)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:N/I:L/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** MEDIUM

While the vulnerability does not directly lead to account takeover, it completely defeats CAPTCHA protections and enables automated abuse of the application.

***

#### 6. Remediation / Mitigation Steps

1. Perform CAPTCHA validation strictly on the server side
2. Invalidate CAPTCHA tokens after single use
3. Bind CAPTCHA values to user sessions
4. Implement rate limiting on form submissions
5. Use modern CAPTCHA solutions (e.g., reCAPTCHA with server verification)
6. Log and monitor suspicious repetitive submissions

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Comment submission form with CAPTCHA input

<figure><img src="../../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Successful submission message despite weak CAPTCHA enforcement

<figure><img src="../../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Comments table showing multiple entries added via CAPTCHA bypass
