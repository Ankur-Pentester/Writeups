# Broken CAPTCHA Validation

#### 1. Vulnerability Name & Description

**Challenge Name:** Broken Captcha

**Broken CAPTCHA – Client-Side Validation Bypass**

The CAPTCHA mechanism is improperly implemented and relies on client-controlled parameters for validation. The arithmetic CAPTCHA values (`num1`, `num2`, and `captcha`) are sent directly from the client and trusted by the server without verification. An attacker can manipulate these parameters to bypass CAPTCHA validation entirely and submit messages successfully.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

[`http://localhost:1337/lab/captcha-bypass/bypass/`](http://localhost:1337/lab/captcha-bypass/bypass/)

**Attack Method:**

CAPTCHA logic manipulation via HTTP request tampering using Burp Suite.

**Steps:**

1. Accessed the message submission form containing a math-based CAPTCHA (Figure 1).
2. Intercepted the POST request using Burp Suite Repeater (Figure 2).
3. Modified the request parameters (`num1`, `num2`, and `captcha`) with arbitrary values.
4. Submitted the manipulated request without solving the CAPTCHA correctly.
5. Server accepted the request and returned a successful verification message (Figure 3).
6. Repeated the attack multiple times to insert arbitrary messages into the system (Figure 4).

***

#### 3. Impact Analysis

* Complete CAPTCHA bypass
* Automated spam and mass message submission
* Bypass of anti-bot protections
* Abuse of application resources
* Potential use in chained attacks (spam, phishing, DoS)

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 6.8 (Medium)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:N/I:L/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** MEDIUM

Although no direct account compromise occurs, the CAPTCHA protection is fully broken, enabling automated abuse and weakening the application’s overall security posture.

***

#### 6. Remediation / Mitigation Steps

1. Perform CAPTCHA validation strictly on the server side
2. Do not trust client-supplied CAPTCHA parameters
3. Generate CAPTCHA challenges server-side and store them in session
4. Invalidate CAPTCHA after a single successful attempt
5. Implement rate limiting and request throttling
6. Use industry-standard CAPTCHA solutions (e.g., reCAPTCHA)

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Message submission form with math-based CAPTCHA

<figure><img src="../../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Intercepted POST request showing modifiable CAPTCHA parameters

<figure><img src="../../../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Successful CAPTCHA verification after request manipulation

<figure><img src="../../../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Stored messages confirming CAPTCHA bypass
