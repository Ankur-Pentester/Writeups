# Learn The Capital 2

## Vulnerability Report : Local File Inclusion with Filter Bypass

**Challenge Name:** Learn The Capital 2

### 1. Vulnerability Name & Description

**Local File Inclusion (LFI) with Path Traversal Filter Bypass**

The application implements basic input filtering to prevent Local File Inclusion attacks by blocking simple path traversal sequences like "../". However, the filter is incomplete and can be bypassed using alternative encoding techniques or sequence variations. Attackers can use encoded path traversal characters or manipulate the filter logic to include arbitrary files from the server filesystem. This vulnerability demonstrates that blacklist-based filtering is insufficient for preventing LFI attacks, as there are numerous bypass techniques available to circumvent poorly implemented filters.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/file-inclusion/learn-the-capital-2/index.php](http://localhost:1337/lab/file-inclusion/learn-the-capital-2/index.php)

**Vulnerable Parameter:** country (GET parameter)

**Normal Functionality:**

* URL: `?country=france.php`
* Displays: "The capital of France is Paris."
* Application includes country-specific PHP files

**Filter Implemented:**

* Application blocks simple "../" path traversal attempts
* Basic blacklist filtering prevents obvious attacks

**Payload Used for Filter Bypass:**

`?country=..../admin.php`

**Attack Steps:**

1. Access the Capital page at [http://localhost:1337/lab/file-inclusion/learn-the-capital-2/index.php](http://localhost:1337/lab/file-inclusion/learn-the-capital-2/index.php)
2. Select "France" from dropdown, observe normal request: `?country=france.php`
3. Attempt simple path traversal: `?country=../admin.php` - Gets blocked by filter
4. Identify filter weakness: it only removes single occurrence of "../"
5. Use double encoding bypass: `?country=..../admin.php`
6. When filter removes "../", it leaves behind "../" from the doubled sequence
7. Filter processes "..../", removes one "../", resulting in "../"
8. Application includes admin.php from parent directory
9. Response displays: "Welcome to the Admin page.."
10. Successfully bypassed filter and accessed restricted admin file

### 3. Impact Analysis

* **Filter Bypass Demonstration:** Proves blacklist-based security controls are ineffective
* **Sensitive File Disclosure:** Attackers can read configuration files, database credentials, and application source code
* **Authentication Bypass:** Access to admin pages and restricted functionality without authorization
* **Information Disclosure:** Exposure of system configuration, internal application structure, and user data
* **Source Code Exposure:** Complete access to application logic revealing additional vulnerabilities
* **Credential Theft:** Database passwords, API tokens, and encryption keys from configuration files
* **False Security:** Developers may believe filter provides protection while vulnerability remains exploitable
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

This vulnerability is particularly concerning as it demonstrates failed security controls. Organizations may have false confidence in their filtering mechanisms while remaining fully vulnerable to LFI attacks through simple bypass techniques.

### 6. Remediation / Mitigation Steps

1. **Never Use Blacklist Filtering:**
   * Blacklist approaches are fundamentally flawed and easily bypassed
   * Attackers can use encoding, variations, and obfuscation techniques
   * Always implement whitelist validation instead
2. **Use Whitelist Validation:**
   * Define allowed country values explicitly
   * Map user input to predefined safe file paths
   * Reject any input not in the whitelist

```bash
*// Secure implementation with whitelist*
$allowed_countries = [
    'france' => 'countries/france.php',
    'germany' => 'countries/germany.php',
    'italy' => 'countries/italy.php'
];

$country = $_GET['country'] ?? 'france';

if (array_key_exists($country, $allowed_countries)) {
    include($allowed_countries[$country]);
} else {
    die("Invalid country selection");
}
```

1. **Use Absolute Paths with Validation:**
   * Construct absolute paths to prevent traversal
   * Use realpath() to resolve path and validate
   * Ensure resolved path is within allowed directory

```bash
$base_dir = '/var/www/html/countries/';
$file = $base_dir . basename($_GET['country']);
$real_path = realpath($file);

if ($real_path && strpos($real_path, $base_dir) === 0) {
    include($real_path);
} else {
    die("Invalid file access");
}
```

1. **Input Sanitization (Defense in Depth):**
   * Use basename() to extract filename only
   * Remove all directory separators
   * Decode URL-encoded input before validation

```bash
$country = $_GET['country'];
$country = urldecode($country); *// Decode first*
$country = basename($country);  *// Extract filename only*
$country = str_replace(['/', '\\\\', '.'], '', $country); *// Remove separators*
```

1. **Disable PHP Wrappers:**
   * Set allow\_url\_include = Off in php.ini
   * Set allow\_url\_fopen = Off for include/require statements
   * Prevents php:// and other dangerous wrapper exploitation
2. **Implement Access Controls:**
   * Protect admin pages with proper authentication and authorization
   * Use session-based access control with role validation
   * Implement principle of least privilege

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Normal functionality shows URL parameter ?country=france.php at learn-the-capital-2, displaying "The capital of France is Paris." demonstrating intended country file inclusion.

<figure><img src="../../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Filter bypass exploitation with payload ?country=..../admin.php successfully circumvents protection, displays "Welcome to the Admin page.." confirming bypassed filter and unauthorized access to restricted admin file through double traversal sequence.

