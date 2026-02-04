# Welcome to Our Gallery

### Vulnerability : Cross-Site Scripting via Image Source Attribute

Challenge Name: Welcome to Our Gallery

#### 1. Vulnerability Name & Description

Cross-Site Scripting via Image src Attribute with onerror Event Handler

The application accepts user input through a URL parameter (img) and directly inserts it into an image tag's src attribute without validation or sanitization. This allows attackers to inject malicious JavaScript using the onerror event handler, which executes when the image fails to load.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/our-gallery/](http://localhost:1337/lab/xss/our-gallery/)

Vulnerable Parameter: img

Payload Used:

`"onerror="alert(document.domain)"`

Full Attack URL:

`http://localhost:1337/lab/xss/our-gallery/?img="onerror="alert(document.domain)"`

Steps:

1. Access the Welcome to Our Gallery page with default parameter ?img=1#
2. Gallery displays four "Image" buttons and shows Mandalorian poster
3. Change URL parameter to ?img=test
4. Page displays broken image (black box) as "test" is not a valid image path
5. Source code (Line 30) shows: \<img src="test.jpg"/>
6. Inject onerror payload in img parameter
7. When image fails to load, onerror event triggers and executes JavaScript
8. Alert box displays "localhost" (document.domain)

Vulnerable Code (Line 30):

`<img class="shadow-lg bg-body rounded" style="width:500px;padding: 0; margin-bottom: 0;" src="test.jpg"/>`

#### 3. Impact Analysis

* JavaScript execution through image error handling
* Session hijacking and cookie theft
* No user interaction required beyond loading the page
* Credential harvesting and data exfiltration
* Can bypass filters that only check for script tags
* Works in contexts where HTML tags are allowed but JavaScript is supposedly blocked

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 6.1 (Medium)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`

#### 5. Risk Rating / Severity

Severity Level: MEDIUM

Image-based XSS is effective as it exploits legitimate HTML functionality and can bypass basic security filters.

#### 6. Remediation / Mitigation Steps

1. Validate image sources - whitelist allowed image paths:

php

_`// Insecure`_` ``$img = $_GET['img']; echo '<img src="' . $img . '.jpg"/>';`

_`// Secure`_` ``$allowedImages = ['1', '2', '3', '4']; $img = $_GET['img']; if (in_array($img, $allowedImages)) { echo '<img src="' . htmlspecialchars($img, ENT_QUOTES, 'UTF-8') . '.jpg"/>'; }`

1. Apply HTML attribute encoding for all user input in attributes
2. Implement Content Security Policy to block inline event handlers
3. Never allow user-controlled input directly in src attributes without validation
4. Use a whitelist approach for allowed image filenames
5. Remove or sanitize event handlers from user input

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Gallery page with ?img=1# showing four "Image" buttons and Mandalorian poster displayed

<figure><img src="../../../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Changed to ?img=test showing broken image (black box) instead of valid image

<figure><img src="../../../.gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Source code showing vulnerable line 30 with direct insertion of user input in img src attribute: "test.jpg"

<figure><img src="../../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Payload injection in URL: ?img="onerror="alert(document.domain)" triggering alert box displaying "localhost"
