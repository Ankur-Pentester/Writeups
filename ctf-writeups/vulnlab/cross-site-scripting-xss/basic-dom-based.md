# Basic DOM-Based

### Vulnerability : DOM-Based Cross-Site Scripting (XSS)

#### 1. Vulnerability Name & Description

Challenge Name: Basic DOM-Based

DOM-Based Cross-Site Scripting (XSS)

The application's client-side JavaScript processes URL parameters and directly manipulates the DOM without proper validation or encoding. User-controlled data from the URL is used in dangerous DOM operations, allowing attackers to inject and execute malicious JavaScript code entirely within the browser.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/basic-dom-based/](http://localhost:1337/lab/xss/basic-dom-based/)

Vulnerable Parameters: height, base

Payload Used:

`<script>alert(9)</script>`

Full Attack URL:

`http://localhost:1337/lab/xss/basic-dom-based/?height=<script>alert(9)<%2Fscript>&base=<script>alert(9)<%2Fscript>#`

**Steps:**

1. Access the triangle area calculator page
2. Enter values (Height: 12, Base: 12) and click Calculate
3. URL parameters are reflected: ?height=12\&base=12#
4. Inject XSS payload in URL parameters
5. JavaScript executes directly from URL without server processing

Vulnerable Code :

`document.getElementById("answer").innerHTML = "Area: "+ans;`

#### 3. Impact Analysis

* Client-side code execution without server interaction
* Session hijacking and cookie theft
* Credential harvesting through fake forms
* Page manipulation and defacement
* Keylogging and user tracking
* Bypasses server-side security filters since payload never reaches server

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 6.1 (Medium)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`

#### 5. Risk Rating / Severity

Severity Level: MEDIUM

DOM-based XSS is harder to detect as the payload never reaches the server, making traditional WAFs ineffective.

#### 6. Remediation / Mitigation Steps

1. Replace innerHTML with textContent for displaying user data:

javascript

_`// Insecure`_` ``element.innerHTML = userInput;`

_`// Secure`_` ``element.textContent = userInput;`

1. Validate and sanitize URL parameters before DOM manipulation
2. Implement Content Security Policy headers
3. Use DOMPurify library for sanitization
4. Avoid eval() and similar dangerous JavaScript functions
5. Encode output when using URL parameters in DOM

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Triangle area calculator interface with Height and Base input fields

<figure><img src="../../../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Calculation result showing "Area: 72" after entering values 12 and 12, URL reflects parameters

<figure><img src="../../../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Source code view showing vulnerable JavaScript on line 35 using innerHTML without sanitization

<figure><img src="../../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

Figure 4: XSS payload in URL (URL-encoded script tags) triggering alert box displaying "9"

