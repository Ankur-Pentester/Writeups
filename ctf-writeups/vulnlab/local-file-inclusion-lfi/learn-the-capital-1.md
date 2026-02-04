# Learn The Capital 1

## Vulnerability Report : Local File Inclusion (LFI)

**Challenge Name:** Learn The Capital 1

### 1. Vulnerability Name & Description

**Local File Inclusion (LFI) via Path Traversal**

The application contains a Local File Inclusion vulnerability in the country parameter that allows attackers to include and read arbitrary files from the server's filesystem. The vulnerability occurs when user-supplied input is used to construct file paths without proper validation or sanitization. Attackers can exploit this by manipulating the country parameter to traverse directories using path traversal sequences (../) and include sensitive system files. This vulnerability enables unauthorized access to configuration files, source code, credentials, and other sensitive information stored on the server.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/file-inclusion/learn-the-capital-1/index.php](http://localhost:1337/lab/file-inclusion/learn-the-capital-1/index.php)

**Vulnerable Parameter:** country (GET parameter)

**Normal Functionality:**

* URL: `?country=france.php`
* Displays: "The capital of France is Paris."
* Application includes country-specific PHP files

**Payload Used for LFI:**

`?country=../admin.php`

**Attack Steps:**

1. Access the Capital page at [http://localhost:1337/lab/file-inclusion/learn-the-capital-1/index.php](http://localhost:1337/lab/file-inclusion/learn-the-capital-1/index.php)
2. Select "France" from dropdown, observe normal request: `?country=france.php`
3. Application displays capital information by including france.php file
4. Modify country parameter to traverse directories: `?country=../admin.php`
5. Application fails to validate input and includes admin.php from parent directory
6. Response displays: "Welcome to the Admin page.."
7. Successfully accessed restricted admin file through path traversal
8. Confirms LFI vulnerability allowing arbitrary file inclusion

### 3. Impact Analysis

* **Sensitive File Disclosure:** Attackers can read configuration files, database credentials, API keys, and application source code
* **Authentication Bypass:** Access to admin pages and restricted functionality without proper authorization
* **Information Disclosure:** Exposure of system configuration, user data, and internal application structure
* **Source Code Exposure:** Complete access to application logic revealing further vulnerabilities
* **Credential Theft:** Database passwords, API tokens, and encryption keys stored in configuration files
* **Log File Analysis:** Access to logs may reveal session tokens, usernames, and system information
* **Confidentiality: HIGH** - Access to sensitive files and restricted pages
* **Integrity: MEDIUM** - With file upload combined with LFI, can achieve Remote Code Execution
* **Availability: LOW** - Generally doesn't affect system availability
* **Privilege Escalation:** Access to admin functionality and sensitive operations
* **Remote Code Execution Risk:** When combined with log poisoning or file upload vulnerabilities
* **Compliance Violations:** Unauthorized access to protected data violates security regulations

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 7.5 (High)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`

### 5. Risk Rating / Severity

**SEVERITY: HIGH**

**Risk Level:** URGENT REMEDIATION REQUIRED

Local File Inclusion vulnerabilities provide direct access to server files and can lead to complete application compromise when combined with other vulnerabilities. The ability to access admin pages and sensitive files makes this a high-priority security issue.

### 6. Remediation / Mitigation Steps

1. **Use Whitelist Validation:**
   * Define an array of allowed country values
   * Map user input to predefined file names
   * Never directly use user input in file paths

```bash
*// Secure implementation with whitelist*
$allowed_countries = [
    'france' => 'france.php',
    'germany' => 'germany.php',
    'italy' => 'italy.php'
];

$country = $_GET['country'] ?? 'france';

if (array_key_exists($country, $allowed_countries)) {
    include($allowed_countries[$country]);
} else {
    die("Invalid country selection");
}
```

1. **Sanitize Input:**
   * Remove path traversal sequences (../, ..)
   * Strip null bytes and special characters
   * Validate file extension

```bash
*// Remove path traversal attempts*
$country = str_replace(['../', '..\\\\', '\\0'], '', $_GET['country']);
$country = basename($country); *// Extract filename only*
```

1. **Use Absolute Paths:**
   * Define base directory for included files
   * Construct absolute paths to prevent directory traversal
   * Verify file exists within allowed directory

```bash
$base_dir = '/var/www/html/countries/';
$file = $base_dir . basename($_GET['country']);

if (realpath($file) && strpos(realpath($file), $base_dir) === 0) {
    include($file);
}
```

1. **Disable PHP Wrappers:**
   * Disable dangerous PHP stream wrappers in php.ini
   * Set allow\_url\_include = Off
   * Set allow\_url\_fopen = Off for include/require
2. **Implement Access Controls:**
   * Protect admin pages with proper authentication
   * Use session-based authorization
   * Implement role-based access control (RBAC)
3. **File System Permissions:**
   * Set restrictive file permissions on sensitive files
   * Run web server with minimal privileges
   * Store sensitive files outside web root

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Normal functionality shows URL parameter ?country=france.php displaying "The capital of France is Paris." demonstrating intended country file inclusion.

<figure><img src="../../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: LFI exploitation with modified parameter ?country=../admin.php displays "Welcome to the Admin page.." confirming successful path traversal and unauthorized access to restricted admin file.
