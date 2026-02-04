# Cross-Site Request Forgery (CSRF) – Password Change

### Vulnerability : Cross-Site Request Forgery (CSRF) – Password Change

#### 1. Vulnerability Name & Description

**Challenge Name:** Changing Password

**Cross-Site Request Forgery (CSRF) – Missing CSRF Protection**

The password change functionality does not implement any CSRF protection mechanisms. The application accepts password change requests via HTTP GET parameters without validating a CSRF token or verifying the request origin. This allows an attacker to trick an authenticated user into changing their password without their consent.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

[`http://localhost:1337/lab/csrf/changing-password/index.php`](http://localhost:1337/lab/csrf/changing-password/index.php)

**Attack Method:**

CSRF via crafted URL containing password change parameters.

**Steps:**

1. Logged in using valid credentials provided by the application (Figure 1).
2. Navigated to the password change page after successful authentication (Figure 2).
3. Entered a new password and confirmed it through the form submission (Figure 3).
4. Observed that the password change request was sent using URL parameters (`new_password` and `confirm_password`) (Figure 4).
5. Replayed the same request directly via browser / chat message without user interaction.
6. Password was successfully changed without CSRF token validation (Figure 5).
7. Logged out and verified that the password had been changed successfully (Figure 6).

***

#### 3. Impact Analysis

* Unauthorized password change
* Account takeover without user awareness
* Exploitation via malicious links or embedded resources
* High risk when combined with phishing attacks
* Complete compromise of user accounts

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 8.8 (High)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** HIGH

This vulnerability enables attackers to fully compromise user accounts by changing passwords without authorization, making it critical in real-world scenarios.

***

#### 6. Remediation / Mitigation Steps

1. Implement CSRF tokens for all state-changing requests
2. Enforce password changes via POST requests only
3. Validate request origin using SameSite cookies
4. Re-authenticate users before sensitive actions
5. Avoid passing sensitive data in URL parameters
6. Use secure frameworks with built-in CSRF protection

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Login page with default credentials

<figure><img src="../../../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Authenticated password change interface

<figure><img src="../../../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>

* Figure 3: New password and confirm password fields submission

<figure><img src="../../../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Network request showing password change via URL parameters

<figure><img src="../../../.gitbook/assets/image (295).png" alt=""><figcaption></figcaption></figure>

* Figure 5: Successful password change confirmation message

<figure><img src="../../../.gitbook/assets/image (296).png" alt=""><figcaption></figcaption></figure>

* Figure 6: Successful login using the newly changed password
