# Path Traversal

## Vulnerability Report : Path Traversal

**Challenge Name:** Path Traversal

### 1. Vulnerability Name & Description

**Path Traversal / Directory Traversal**

Path Traversal, also known as Directory Traversal, is a web security vulnerability that allows attackers to read arbitrary files on the server by manipulating file path parameters. The vulnerability occurs when an application uses user-supplied input to construct file paths without proper validation or sanitization. Attackers exploit this by using special character sequences like "../" (dot-dot-slash) to navigate up the directory structure and access files outside the intended directory. This can lead to exposure of sensitive system files, application source code, configuration files containing credentials, and other confidential data. The vulnerability exists because the application directly passes user input to file system operations without implementing proper path canonicalization or validation.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/path-traversal/path-traversal-2/product-detail.php](http://localhost:1337/lab/path-traversal/path-traversal-2/product-detail.php)

**Vulnerable Parameter:** productId (GET parameter)

**Normal Functionality:**

* URL: `?productId=1.png`
* Displays product image and description about Robinson Crusoe book
* Application loads image from file system based on productId parameter

**Initial Discovery:**

1. Access Path Traversal page at [http://localhost:1337/lab/path-traversal/path-traversal-2/](http://localhost:1337/lab/path-traversal/path-traversal-2/)
2. Navigate to product details with normal parameter: `?productId=1.png`
3. Observe page loads product image successfully
4. View page source code to understand file path construction
5. Source shows: `<img src="img/../../1.png">` indicating relative path usage
6. Identifies vulnerable parameter that constructs file paths

**Path Traversal Exploitation:**

**Payload Used:**

`?productId=../../../../../../etc/passwd`

**Attack Steps:**

1. Modify productId parameter to include path traversal sequences
2. Navigate to: `?productId=../../../../../../etc/passwd`
3. Application constructs path: `img/../../../../../../etc/passwd`
4. Multiple "../" sequences traverse up directory structure
5. Final resolved path reaches system root and accesses /etc/passwd
6. Page displays complete contents of /etc/passwd file
7. Output shows all system users including root, daemon, bin, sys, www-data, mysql, etc.
8. Successfully read sensitive system file through path traversal
9. Confirms arbitrary file read vulnerability

### 3. Impact Analysis

* **Sensitive File Disclosure:** Unauthorized access to system files including /etc/passwd, /etc/shadow, configuration files
* **Credential Exposure:** Database passwords, API keys, encryption keys stored in configuration files
* **Source Code Disclosure:** Access to application source code revealing business logic and additional vulnerabilities
* **System Information Leakage:** Operating system details, installed software, network configuration
* **Authentication Bypass:** Access to authentication configuration and credential storage mechanisms
* **Privilege Escalation Path:** Information gained can be used for further attacks and privilege escalation
* **Configuration File Access:** Web server configs, application configs, database configs containing sensitive data
* **Confidentiality: HIGH** - Complete read access to any file accessible by web server user
* **Integrity: LOW** - Read-only vulnerability, no direct modification capabilities
* **Availability: LOW** - Generally doesn't affect system availability
* **Business Impact:** Data breach, exposure of trade secrets, compliance violations, reputational damage
* **Lateral Movement:** Information from config files enables attacks on databases and other systems

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 7.5 (High)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`

### 5. Risk Rating / Severity

**SEVERITY: HIGH**

**Risk Level:** URGENT REMEDIATION REQUIRED

Path Traversal vulnerabilities provide direct access to sensitive files and can expose credentials leading to complete system compromise. This requires urgent remediation to prevent unauthorized data access.

### 6. Remediation / Mitigation Steps

1. **Use Whitelist Validation:**
   * Define allowed file names or IDs explicitly
   * Map user input to predefined safe file paths
   * Never directly use user input in file paths
   * Validate against strict patterns (alphanumeric only)
2. **Implement Path Canonicalization:**
   * Use realpath() to resolve absolute path
   * Verify resolved path is within allowed base directory
   * Reject any path that escapes base directory
   * Remove all path traversal sequences before validation
3. **Input Sanitization:**
   * Strip or reject "../", "..", and encoded variations
   * Remove null bytes and special characters
   * Validate file extension matches expected type
   * Use basename() to extract filename only
4. **Use Absolute Paths:**
   * Define base directory with absolute path
   * Construct full paths programmatically
   * Never concatenate user input directly
   * Verify final path starts with base directory
5. **File System Restrictions:**
   * Run application with minimal file system permissions
   * Use chroot jails or containers to limit filesystem access
   * Implement proper file permissions on sensitive files
   * Store sensitive files outside web root
6. **Web Application Firewall (WAF):**
   * Deploy WAF rules to detect traversal attempts
   * Monitor for "../" sequences in parameters
   * Block encoded traversal attempts
   * Alert on suspicious file access patterns

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Product detail page with URL parameter ?productId=1.png displays product image and Robinson Crusoe book description demonstrating normal functionality.

<figure><img src="../../../.gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Viewing page source reveals  showing vulnerable file path construction with relative paths, confirming productId parameter is used directly in file operations.

<figure><img src="../../../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Path traversal exploitation with payload ?productId=../../../../../../etc/passwd displays complete /etc/passwd file contents including all system users (root, daemon, bin, sys, www-data, mysql, etc.) confirming successful arbitrary file read vulnerability.
