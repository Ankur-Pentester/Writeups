# Authentication Bypass (No Redirect)

#### 1. Vulnerability Name & Description

**Challenge Name:** No Redirect

**Authentication Bypass – Broken Authentication**

The application relies only on client-side redirection to protect authenticated resources. Although unauthenticated users are redirected to the login page, the backend still returns sensitive content in the HTTP response. By directly accessing protected endpoints, an attacker can view restricted content without successful authentication.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

[`http://localhost:1337/lab/broken-authentication/no-redirect/index.php`](http://localhost:1337/lab/broken-authentication/no-redirect/index.php)

**Attack Method:**

Direct access to protected resource and interception of server response using Burp Suite.

**Steps:**

1. Accessed the login page and observed a hint indicating `index.php` could be accessed directly (Figure 1).
2. Navigated directly to `/no-redirect/index.php` without authentication.
3. Intercepted the request using Burp Suite Proxy (Figure 2).
4. Server responded with **HTTP 302 redirect** to `login.php`, but still included protected content in the response body (Figure 3).
5. Viewed the rendered response in Burp, revealing admin panel content without logging in (Figure 4).

***

#### 3. Impact Analysis

* Unauthorized access to protected application pages
* Exposure of admin or internal functionality
* Complete authentication bypass
* Information disclosure of sensitive interfaces
* Increased risk of privilege escalation and further attacks

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 8.0 (High)

**Vector String:**

`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`

***

#### 5. Risk Rating / Severity

**Severity Level:** HIGH

The vulnerability allows attackers to access sensitive content without valid credentials due to improper server-side access control enforcement.

***

#### 6. Remediation / Mitigation Steps

1. Enforce authentication checks on the server side for all protected resources
2. Do not return sensitive content before authorization validation
3. Implement proper access control middleware
4. Return `403 Forbidden` for unauthorized access instead of redirecting
5. Ensure redirects do not leak protected data in HTTP responses

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (287).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Login page indicating direct access to `index.php`

<figure><img src="../../../.gitbook/assets/image (288).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Intercepted unauthenticated request to protected endpoint

<figure><img src="../../../.gitbook/assets/image (289).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Server response showing HTTP 302 with sensitive content

<figure><img src="../../../.gitbook/assets/image (290).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Rendered admin/dashboard content accessible without login
