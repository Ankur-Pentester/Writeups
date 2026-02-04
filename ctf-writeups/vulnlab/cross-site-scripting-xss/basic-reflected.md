# Basic Reflected

### Vulnerability : Basic Reflected Cross-Site Scripting (XSS)

#### 1. Vulnerability Name & Description

Challenge Name: Basic Reflected

Reflected Cross-Site Scripting (XSS) - Basic

The application suffers from a Reflected Cross-Site Scripting vulnerability in the search functionality. User-supplied input is reflected back in the HTTP response without proper sanitization or encoding, allowing an attacker to inject malicious JavaScript code that executes in the victim's browser context.

#### 2. Proof of Concept (PoC)

Vulnerable Parameter: q (search query parameter)

Attack Vector:

`http://localhost:1337/lab/xss/basic-reflected/?q=<script>alert(9)</script>`

Payload Used:

`<script>alert(9)</script>`

Execution Flow:

1. Attacker crafts a malicious URL containing JavaScript code in the search parameter
2. The crafted URL is sent to the victim (via phishing, social engineering, etc.)
3. When the victim clicks the link, the application reflects the malicious script without sanitization
4. The JavaScript code executes in the victim's browser, displaying an alert box with the value "9"
5. The source code reveals the payload is directly embedded in the HTML without any encoding

#### 3. Impact Analysis

The successful exploitation of this vulnerability can lead to:

* Session Hijacking: Attackers can steal session cookies and authentication tokens, leading to complete account takeover
* Credential Theft: Malicious scripts can create fake login forms to capture user credentials
* Malware Distribution: Victims can be redirected to malicious websites hosting drive-by downloads
* Defacement: The attacker can modify page content visible to the victim
* Phishing Attacks: Creation of convincing phishing pages within the trusted domain context
* Keylogging: Capture of sensitive information through injected keyloggers
* Data Exfiltration: Access to sensitive information visible on the page or accessible through DOM manipulation

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 6.1 (Medium)

Vector String:

`CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`

Metrics Breakdown:

* Attack Vector (AV:N): Network - exploitable remotely
* Attack Complexity (AC:L): Low - no special conditions required
* Privileges Required (PR:N): None - no authentication needed
* User Interaction (UI:R): Required - victim must click the malicious link
* Scope (S:C): Changed - affects resources beyond the vulnerable component
* Confidentiality (C:L): Low - some information disclosure possible
* Integrity (I:L): Low - limited ability to modify data
* Availability (A:N): None - no impact on availability

#### 5. Risk Rating / Severity

Severity Level: MEDIUM

While the CVSS score indicates a medium severity, the actual risk depends on:

* The sensitivity of data accessible through the application
* Whether authenticated users are targeted
* The presence of other security controls (CSP, HttpOnly cookies, etc.)
* The application's user base and exposure

#### 6. Remediation / Mitigation Steps

Immediate Actions:

1. Input Validation:
   * Implement whitelist-based input validation
   * Reject inputs containing script tags or JavaScript event handlers
   * Limit allowed characters in search functionality
2. Output Encoding:
   * Apply context-appropriate output encoding for all user-supplied data
   * Use HTML entity encoding for data reflected in HTML context:
     * < becomes <
     * becomes >
     * " becomes "
     * ' becomes '
     * / becomes /
3. Content Security Policy (CSP):
   * Implement strict CSP headers to prevent inline script execution

`Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none'`

1. HTTPOnly and Secure Cookie Flags:
   * Set HTTPOnly flag on session cookies to prevent JavaScript access
   * Enable Secure flag to ensure cookies are only transmitted over HTTPS

Long-term Solutions:

1. Use security frameworks and templating engines that automatically escape output
2. Implement Web Application Firewall (WAF) rules to detect and block XSS attempts
3. Regular security testing and code reviews focusing on input/output handling
4. Security awareness training for development teams on secure coding practices
5. Consider implementing X-XSS-Protection header (though deprecated, still provides defense-in-depth)

Example Secure Code (PHP):

`// Insecure (current implementation) echo "" . $_GET['q'] . "";`

`// Secure implementation echo "" . htmlspecialchars($_GET['q'], ENT_QUOTES, 'UTF-8') . "";`

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

* Displays the search functionality where the payload "test" was initially entered
* URL shows the query parameter being reflected in the address bar

<figure><img src="../../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

* Demonstrates the search query being reflected in the URL: ?q=test#
* Shows "No Result Found for test" message with "Try Again" link
* Confirms that user input is being processed and reflected back to the user

<figure><img src="../../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

* View-source page revealing the vulnerable code structure
* Line 17 shows the direct reflection of user input without sanitization
* Confirms absence of input sanitization or output encoding mechanisms

<figure><img src="../../../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

* JavaScript alert box displaying "9" confirming successful code execution
* URL shows the malicious payload: ?q=alert(9)#
* Demonstrates that arbitrary JavaScript can be executed in the victim's browser context
* The alert execution proves the vulnerability is exploitable and poses a real security risk
