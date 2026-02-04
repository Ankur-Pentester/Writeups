# News

### Vulnerability : Stored Cross-Site Scripting via Anchor Tag href Attribute

Challenge Name: News

#### 1. Vulnerability Name & Description

Stored Cross-Site Scripting (XSS) via Anchor Tag href Attribute with JavaScript Protocol

The application allows users to submit news items with titles and URLs. The URL input is stored in the database and rendered as an anchor tag's href attribute without proper validation. This allows attackers to inject JavaScript code using the javascript: protocol, which executes when users click on the link.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/news/index.php](http://localhost:1337/lab/xss/news/index.php)

Vulnerable Parameters: News Title, News Url

Payload Used:

`javascript:alert(document.domain)`

Attack Steps:

1. Access the News challenge page
2. Form displays "You Can Add News too" with News Title and News Url input fields
3. Enter "test" in both fields and click Submit
4. News entry appears in table "News All Around The World" with clickable "test" link
5. Source code (Line 56) shows: \<a href="test">test\</a>
6. Enter "XSS" as News Title
7. Enter "javascript:alert(document.domain)" as News Url
8. Click Submit button
9. New entry "XSS" appears as clickable link in the news table
10. When user clicks on "XSS" link, JavaScript executes
11. Alert box displays "localhost"

Vulnerable Code (Line 56):

`<a href="test">test</a>`

#### 3. Impact Analysis

* Persistent XSS affecting all users who click the malicious link
* Session hijacking through cookie theft
* Credential harvesting via fake login prompts
* Phishing attacks using trusted domain context
* Malware distribution and drive-by downloads
* Account takeover of users clicking the link
* Bypasses filters that only check for script tags
* Social engineering can trick users into clicking malicious news links

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 7.1 (High)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:C/C:L/I:L/A:L`

#### 5. Risk Rating / Severity

Severity Level: HIGH

While this requires user interaction (clicking the link), it's highly effective as users naturally click on news links, making exploitation likely.

#### 6. Remediation / Mitigation Steps

1. Validate and sanitize URL inputs - whitelist allowed protocols:

php

_`// Insecure`_` ``$url = $_POST['url']; echo '<a href="' . $url . '">' . $title . '</a>';`

_`// Secure`_` ``$url = $_POST['url']; $allowedProtocols = ['http', 'https']; $parsedUrl = parse_url($url); if (isset($parsedUrl['scheme']) && in_array($parsedUrl['scheme'], $allowedProtocols)) { echo '<a href="' . htmlspecialchars($url, ENT_QUOTES, 'UTF-8') . '">' . htmlspecialchars($title, ENT_QUOTES, 'UTF-8') . '</a>'; }`

1. Block javascript: protocol and other dangerous protocols (data:, vbscript:, file:)
2. Apply HTML attribute encoding for all user input
3. Implement Content Security Policy to restrict script execution
4. Use URL validation libraries to ensure proper URL format
5. Add rel="noopener noreferrer" to external links for additional security

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (342).png" alt=""><figcaption></figcaption></figure>

* Figure 1: News submission form with "test" entered in both News Title and News Url fields, showing Submit button

<figure><img src="../../../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Source code view showing vulnerable line 56 with anchor tag: test without sanitization

<figure><img src="../../../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Form filled with "XSS" as News Title and "javascript:alert(document.domain)" as News Url payload

<figure><img src="../../../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>

* Figure 4: News table showing two entries - "test" (entry #1) and "XSS" (entry #2) as clickable links with up/down arrows

<figure><img src="../../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

* Figure 5: Alert box displaying "localhost" after clicking the "XSS" link, confirming successful stored XSS exploitation via javascript: protocol in href attribute
