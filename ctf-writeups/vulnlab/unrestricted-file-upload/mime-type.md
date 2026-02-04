# MIME Type

## Vulnerability Report : File Upload - MIME Type Bypass

**Challenge Name:** MIME Type

### 1. Vulnerability Name & Description

**File Upload MIME Type Validation Bypass**

The application implements file upload restrictions based on MIME type validation, checking the Content-Type header to ensure only image files (gif, jpg, jpeg, png) are uploaded. However, this validation mechanism is fundamentally flawed as the Content-Type header is client-controlled and can be easily manipulated by attackers. By intercepting the upload request and changing the Content-Type from "application/x-php" to "image/png" or another allowed image MIME type, attackers can bypass the validation and upload malicious executable files such as PHP web shells. The server trusts the client-provided MIME type without performing actual file content validation (magic byte verification), allowing arbitrary file uploads that lead to Remote Code Execution.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/file-upload/mime-type/](http://localhost:1337/lab/file-upload/mime-type/)

**Vulnerable Parameter:** File upload form field with MIME type validation

**Stated Restrictions:** "Allowed formats: gif, jpg, jpeg, png"

**Malicious File Created:**

`<?php system($_GET['cmd']); ?>`

Filename: cmd.php

Attack Steps:\`

1. Access MIME Type upload page at [http://localhost:1337/lab/file-upload/mime-type/](http://localhost:1337/lab/file-upload/mime-type/)
2. Observe upload form restricts to image formats: gif, jpg, jpeg, png
3. Create malicious PHP web shell file named cmd.php with command execution payload
4. Select cmd.php file in upload form
5. Intercept upload request using Burp Suite Repeater
6. Observe original request shows filename="cmd.php" with Content-Type header
7. Modify Content-Type header from application/x-php to image/png
8. Server validates only Content-Type header, not actual file content
9. Submit modified request with manipulated MIME type
10. Application accepts the upload based on Content-Type alone
11. Success message displays: "File uploaded successfully!"
12. File path shown: "uploads/cmd.php"
13. Access uploaded web shell at /uploads/cmd.php?cmd=id
14. Execute command through GET parameter
15. Output displays server user context: uid=33(www-data) gid=33(www-data) groups=33(www-data)
16. Successfully achieved Remote Code Execution through MIME type bypass

\`MIME Type Manipulation Examples:

Content-Type: image/png Content-Type: image/jpeg Content-Type: image/gif Content-Type: image/jpg\`

### 3. Impact Analysis

* **Remote Code Execution (RCE):** Complete server compromise through uploaded malicious scripts
* **MIME Type Security Failure:** Demonstrates that client-provided headers cannot be trusted
* **Web Shell Deployment:** Persistent backdoor access for ongoing attacks
* **Server Takeover:** Full control with ability to execute arbitrary system commands
* **Data Exfiltration:** Access to databases, configuration files, user data, and sensitive information
* **Malware Distribution:** Server can be used to host and distribute malware
* **Privilege Escalation:** Potential to escalate from www-data to root privileges
* **Lateral Movement:** Compromised server becomes pivot point for internal network attacks
* **Confidentiality: CRITICAL** - Complete access to all server files and data
* **Integrity: CRITICAL** - Ability to modify any accessible file and inject malicious code
* **Availability: CRITICAL** - Can crash services, delete files, or launch DoS attacks
* **Business Impact:** Complete application compromise, data breach, service disruption, compliance violations, reputational damage

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED - EMERGENCY

MIME type validation bypass leading to RCE is a critical vulnerability that provides attackers with complete server control. This requires immediate emergency remediation as it represents complete application compromise.

### 6. Remediation / Mitigation Steps

1. **Never Trust Client-Provided Data:**
   * Content-Type header is completely client-controlled and should never be trusted
   * MIME type validation alone is completely insufficient for security
   * Always validate actual file content using server-side methods
2. **Implement File Content Validation:**
   * Validate file magic bytes/signatures (first few bytes of file content)
   * Use server-side libraries like fileinfo (PHP) to detect actual file type
   * Reject files where content doesn't match expected image format
   * Verify image integrity by attempting to load with image processing libraries
3. **Rename All Uploaded Files:**
   * Generate random filenames using secure methods (UUID, cryptographic hash)
   * Remove original file extension completely
   * Store original filename in database separately if needed
   * Prevent execution by eliminating executable extensions
4. **Store Files Outside Web Root:**
   * Store uploaded files in directories outside web-accessible paths
   * Serve files through application script with proper access controls
   * Implement authentication and authorization checks before serving files
   * Use X-Sendfile or similar mechanisms for secure file delivery
5. **Disable Script Execution in Upload Directory:**
   * Configure web server to prevent PHP/script execution in upload folders
   * Add .htaccess or nginx configuration rules to deny execution
   * Set directory permissions to remove execute permissions
   * Use separate domain or subdomain for serving uploaded content
6. **Additional Security Measures:**
   * Implement strict file size limits
   * Use antivirus scanning on all uploaded files
   * Apply rate limiting to prevent upload abuse
   * Log all upload attempts for security monitoring and incident response

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: MIME Type upload page shows cmd.php file selected with "Allowed formats: gif, jpg, jpeg, png" restriction indicating MIME type-based validation.

<figure><img src="../../../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Burp Suite intercepts upload request showing Content-Type: image/png header manipulation for cmd.php file, with success message "File uploaded successfully!" and path "uploads/cmd.php" confirming MIME type validation was bypassed.

<figure><img src="../../../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Accessing uploaded web shell at /uploads/cmd.php?cmd=id displays "uid=33(www-data) gid=33(www-data) groups=33(www-data)" confirming successful Remote Code Execution through MIME type bypass vulnerability.
