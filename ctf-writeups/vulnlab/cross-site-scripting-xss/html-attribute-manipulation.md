# HTML Attribute Manipulation

### Vulnerability : HTML Attribute Manipulation XSS

#### 1. Vulnerability Name & Description

Cross-Site Scripting via HTML Attribute Manipulation

The application accepts user input through a URL parameter (name) and directly embeds it into an HTML attribute without proper encoding or validation. This allows attackers to break out of the attribute context and inject malicious JavaScript code through event handlers like onmouseover.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/html-attribute-manipulation/](http://localhost:1337/lab/xss/html-attribute-manipulation/)

Vulnerable Parameter: name

Payload Used:

`"onmouseover="alert(1)%2F%2F`

Full Attack URL:

`http://localhost:1337/lab/xss/html-attribute-manipulation/?name="onmouseover%3Dalert(1)%2F%2F`

**Steps:**

1. Access the Mandalorian Movie Tickets page
2. Enter "test" in the Name field and click "GET THE TICKET"
3. URL parameter reflects: ?name=test#
4. Source code (Line 18) shows: \<h2> test's \</h2>
5. Inject attribute breakout payload in the name parameter
6. Payload breaks out of the attribute and injects onmouseover event handler
7. When user hovers over the element, alert box displays "1"

Vulnerable Code :

`<h2> test's </h2>`

#### 3. Impact Analysis

* JavaScript execution through HTML event handlers
* Session hijacking and cookie theft
* Credential harvesting via fake forms
* User interaction required (mouseover) makes it slightly less severe than reflected XSS
* Can be combined with social engineering for effective exploitation
* Bypasses basic XSS filters that only look for script tags

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 6.1 (Medium)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`

#### 5. Risk Rating / Severity

Severity Level: MEDIUM

This variant requires user interaction (hovering) but can still compromise user sessions and data effectively.

#### 6. Remediation / Mitigation Steps

1. Apply HTML attribute encoding for all user input placed in attributes:

_`// Insecure`_` ``echo '<h2>' . $_GET['name'] . "'s</h2>";`

_`// Secure`_` ``echo '<h2>' . htmlspecialchars($_GET['name'], ENT_QUOTES, 'UTF-8') . "'s</h2>";`

2. Implement strict input validation - reject special characters like quotes and angle brackets

&#x20;3\. Use Content Security Policy to prevent inline event handlers:

`Content-Security-Policy: script-src 'self'`

1. Sanitize URL parameters on both client and server side
2. Never place user input directly in HTML attributes without encoding
3. Use templating engines with automatic escaping

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Mandalorian Movie Tickets form with Name input field and "GET THE TICKET" button, "SEE YOUR TICKET" link below

<figure><img src="../../../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

* Figure 2: After submitting "test", URL shows ?name=test# parameter and displays "test's" heading with movie poster

<figure><img src="../../../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Source code showing vulnerable line 18 with direct insertion of user input:\<h2>test\</h2>

<figure><img src="../../../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Payload injection with `"onmouseover=alert(1)//"` visible in the Name field

<figure><img src="../../../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

* Figure 5: Alert box displaying "1" triggered by mouseover event, confirming successful attribute-based XSS exploitation



