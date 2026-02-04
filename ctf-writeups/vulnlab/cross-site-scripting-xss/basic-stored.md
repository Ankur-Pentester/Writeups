# Basic Stored

### Vulnerability : Stored Cross-Site Scripting (XSS)

#### 1. Vulnerability Name & Description

Challenge Name: Basic Stored

Stored Cross-Site Scripting (XSS) - Persistent

The messaging functionality stores user input in the database without sanitization and renders it without encoding. Malicious JavaScript persists and executes automatically for all users viewing the page.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/basic-stored/stored.php](http://localhost:1337/lab/xss/basic-stored/stored.php)

Credentials Used: mandalorian / mandalorian

Payload:

`<script>alert(9)</script>`

Steps:

1. Login with credentials
2. Submit payload in message textarea
3. Payload stored in database
4. Script executes automatically when page loads for any user

#### 3. Impact Analysis

* Session hijacking and account takeover for all users
* Automatic execution without user interaction
* Persistent backdoor affecting multiple victims
* Privilege escalation if admin views the page
* Data theft and credential harvesting

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 7.1 (High)

Vector String:

`CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:L/I:L/A:L`

#### 5. Risk Rating / Severity

Severity Level: HIGH

More dangerous than reflected XSS due to persistence and automatic execution affecting all users.

#### 6. Remediation / Mitigation Steps

Apply HTML encoding on output:

`echo htmlspecialchars($message, ENT_QUOTES, 'UTF-8');`

1. Validate input - reject HTML/JavaScript syntax
2. Implement Content Security Policy headers
3. Set HTTPOnly flag on session cookies
4. Clean existing database of malicious entries
5. Use secure templating engines with auto-escaping

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Login page with credentials (mandalorian/mandalorian)

<figure><img src="../../../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Message submission interface with warning "All Users Can See Your Message"

<figure><img src="../../../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Test message stored and displayed successfully

<figure><img src="../../../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Source code showing vulnerable rendering on line 21 without encoding

<figure><img src="../../../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

* Figure 5: XSS payload `<script>alert(9)</script>` entered in text area

<figure><img src="../../../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

* Figure 6: Alert box displaying "9" confirming successful stored XSS exploitation
