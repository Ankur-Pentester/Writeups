# Learn The Capital 3

## Vulnerability Report : Local File Inclusion with Extension Bypass

**Challenge Name:** Learn The Capital 3

### 1. Vulnerability Name & Description

**Local File Inclusion (LFI) with File Extension Filter Bypass**

The application implements input filtering that enforces a specific file extension (.php) to prevent Local File Inclusion attacks. The filter appends ".php" to user input or validates that the input ends with ".php" extension. However, this protection can be bypassed using null byte injection (in older PHP versions) or by manipulating the file path structure with path traversal sequences followed by specific file naming. Attackers can exploit the filter logic to include arbitrary files by crafting payloads that satisfy the extension requirement while still accessing unintended files. This vulnerability demonstrates that extension-based filtering alone is insufficient to prevent LFI attacks.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/file-inclusion/learn-the-capital-3/index.php](http://localhost:1337/lab/file-inclusion/learn-the-capital-3/index.php)

**Vulnerable Parameter:** country (GET parameter)

**Normal Functionality:**

* URL: `?country=france.php`
* Displays: "The capital of France is Paris."
* Application includes country-specific PHP files

**Filter Implemented:**

* Application enforces .php extension requirement
* May append .php automatically or validate extension
* Intended to prevent access to non-PHP files

**Initial Attempt:**

* Payload: `?country=france.php` - Works normally
* Error displayed: "ERROR: File not found!" when trying simple path traversal

**Payload Used for Filter Bypass:**

`?country=file/../admin.php`

**Attack Steps:**

1. Access the Capital page at [http://localhost:1337/lab/file-inclusion/learn-the-capital-3/index.php](http://localhost:1337/lab/file-inclusion/learn-the-capital-3/index.php)
2. Select "France" from dropdown, observe normal request: `?country=france.php`
3. Attempt simple path traversal: `?country=../admin.php` - Gets error "ERROR: File not found!"
4. Analyze filter: application validates extension or expects file in specific directory
5. Craft bypass payload: `?country=file/../admin.php`
6. The "file/" part satisfies directory expectations or validation logic
7. The "../" traverses back to parent directory
8. Final path resolves to: admin.php in parent directory
9. Application includes admin.php successfully
10. Response displays: "Welcome to the Admin page.."
11. Successfully bypassed extension filter and accessed restricted admin file

### 3. Impact Analysis

* **Extension Filter Bypass:** Demonstrates that extension-based validation is insufficient
* **Sensitive File Disclosure:** Attackers can read configuration files, source code, and credentials
* **Authentication Bypass:** Access to admin pages and restricted functionality without authorization
* **Information Disclosure:** Exposure of system configuration, internal application structure, and user data
* **Source Code Exposure:** Complete access to application logic revealing additional vulnerabilities
* **Credential Theft:** Database passwords, API tokens, and encryption keys from configuration files
* **System File Access:** Potential access to /etc/passwd and other system files
* **Confidentiality: HIGH** - Access to sensitive files and restricted administrative pages
* **Integrity: MEDIUM** - Potential for Remote Code Execution when combined with file upload
* **Availability: LOW** - Generally doesn't directly affect system availability
* **Privilege Escalation:** Unauthorized access to admin functionality and sensitive operations
* **Compliance Violations:** Unauthorized access to protected data violates security regulations

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 7.5 (High)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`

### 5. Risk Rating / Severity

**SEVERITY: HIGH**

**Risk Level:** URGENT REMEDIATION REQUIRED

This vulnerability demonstrates that extension-based filtering provides false security. Even with extension validation, attackers can manipulate file paths to access restricted files and bypass security controls.

### 6. Remediation / Mitigation Steps

1. **Use Whitelist Validation:**
   * Define explicit list of allowed values only
   * Map user input to predefined safe file paths
   * Never rely on extension validation alone
2. **Implement Absolute Path Validation:**
   * Use realpath() to resolve full file paths
   * Verify resolved path is within allowed base directory
   * Reject any path that escapes the allowed directory
3. **Sanitize Input:**
   * Use basename() to extract filename only and remove directory components
   * Remove all path traversal sequences (.., ./, .)
   * Validate input against alphanumeric patterns only
4. **Disable Dangerous PHP Features:**
   * Set allow\_url\_include = Off in php.ini
   * Set allow\_url\_fopen = Off for include/require
   * Update PHP to latest version (fixes null byte injection)
5. **Apply Defense in Depth:**
   * Set restrictive file permissions on sensitive files
   * Implement Web Application Firewall (WAF) rules
   * Monitor and log all file access attempts for suspicious patterns

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Initial attempt with URL parameter ?country=france.php at learn-the-capital-3 displays "ERROR: File not found!" indicating filter is blocking standard requests differently than previous versions.

<figure><img src="../../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Successful bypass using payload ?country=file/../admin.php circumvents extension filter, displays "Welcome to the Admin page.." confirming path manipulation bypassed protection and gained unauthorized access to restricted admin file
