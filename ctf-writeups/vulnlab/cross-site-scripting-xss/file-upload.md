# File Upload

### Vulnerability #8: Stored Cross-Site Scripting via File Upload

Challenge Name: File Upload

#### 1. Vulnerability Name & Description

Stored Cross-Site Scripting (XSS) via Malicious File Upload

The application allows users to upload profile pictures without proper file validation or content sanitization. Attackers can upload files with malicious filenames containing XSS payloads. When the filename is displayed or processed by the application, the embedded JavaScript code executes in the victim's browser.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/image-upload/](http://localhost:1337/lab/xss/image-upload/)

Vulnerable Parameter: File upload (filename)

Payload Used:

`"><img src=x o..t("Hack")>.png`

Attack Steps:

1. Access the File Upload challenge page
2. Profile shows "Welcome mandalorian" with user details (Name: Din, Surname: Djarin, Age: 36, Profession: Bounty Hunter)
3. File upload section shows "Change Profile picture" with Browse button and filename "test.jpg"
4. Upload a normal image first to verify functionality
5. Create a malicious filename containing XSS payload: ">\<img src=x o..t("Hack")>.png
6. Browse and select file with malicious filename
7. Click Upload button
8. Application processes the filename without sanitization
9. When page loads or displays the filename, XSS payload executes
10. Alert box displays "Hack"

Vulnerable Processing: The application directly uses the uploaded filename in HTML context without encoding, allowing the injected img tag with onerror handler to execute.

#### 3. Impact Analysis

* Persistent XSS affecting all users viewing the uploaded file information
* Session hijacking and cookie theft
* Account takeover of users accessing the page
* Malware distribution through compromised accounts
* Credential harvesting via fake prompts
* File upload functionality often trusted by users, making exploitation easier
* Can bypass content-based XSS filters by hiding payload in metadata
* Affects file management and admin panels viewing uploaded files

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 7.1 (High)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:L/I:L/A:L`

#### 5. Risk Rating / Severity

Severity Level: HIGH

File upload XSS is particularly dangerous as it combines file upload vulnerabilities with stored XSS, and filenames are often overlooked during security reviews.

#### 6. Remediation / Mitigation Steps

1. Sanitize and validate filenames before storage:

_`// Insecure`_` ``$filename = $_FILES['file']['name']; move_uploaded_file($_FILES['file']['tmp_name'], $uploadDir . $filename);`

_`// Secure`_` ``$filename = basename($`_`FILES['file']['name']); $filename = preg_replace("/[^a-zA-Z0-9.`_`-]/", "", $filename); $uniqueFilename = uniqid() . '_' . $filename; move_uploaded_file($_FILES['file']['tmp_name'], $uploadDir . $uniqueFilename);`

1. Rename all uploaded files to randomly generated names, storing original filename separately in database
2. Apply HTML encoding when displaying filenames:

`echo htmlspecialchars($filename, ENT_QUOTES, 'UTF-8');`

1. Validate file extensions using whitelist approach (jpg, png, gif only for images)
2. Check file MIME type and actual file content, not just extension
3. Store uploaded files outside web root with restricted access
4. Implement Content Security Policy to prevent inline script execution
5. Set proper Content-Type and Content-Disposition headers when serving files

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>

* Figure 1: File upload interface showing profile picture upload with "test.jpg" selected, displaying "Welcome mandalorian" profile with personal details

<figure><img src="../../../.gitbook/assets/image (348).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Malicious filename visible in upload field: ">\<img src=x o..t("Hack")>.png with Browse button and Upload button

<figure><img src="../../../.gitbook/assets/image (349).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Alert box displaying "Hack" confirming successful stored XSS exploitation via malicious filename<br>
