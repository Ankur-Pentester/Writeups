# Stock Control

## Vulnerability Report : OS Command Injection in Stock Control

**Challenge Name:** Stock Control

### 1. Vulnerability Name & Description

**OS Command Injection via URL Parameter Manipulation**

The Stock Control application contains a critical command injection vulnerability in the `product_id` URL parameter. The application fails to properly validate and sanitize user input before passing it to system commands for stock checking. Attackers can manipulate the `product_id` parameter to inject arbitrary OS commands that execute on the server with web application privileges (www-data). The vulnerability allows direct parameter manipulation through the URL without requiring form submission, making it trivially exploitable.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/command-injection/stock-check/](http://localhost:1337/lab/command-injection/stock-check/)

**Vulnerable Parameter:** product\_id (GET parameter)

**Payload Used:**

`?product_id=id`

**Attack Steps:**

1. Access the Stock Control page at [http://localhost:1337/lab/command-injection/stock-check/](http://localhost:1337/lab/command-injection/stock-check/)
2. Click any "Check" button to observe normal functionality
3. Normal request: ?product\_id=1 displays "Stock: 13 Pieces"
4. Modify URL directly to inject command: ?product\_id=id
5. Server executes injected command without validation
6. Backend likely executes something like: [stockcheck.sh](http://stockcheck.sh) id or cat stock\_id.txt
7. Output displays in stock display area: Stockuid=33(www-data) gid=33(www-data) groups=33(www-data) Pieces
8. Successfully executed id command, revealing server user context

**Alternative Exploitation Payloads:**

```bash
?product_id=1;id
?product_id=1|whoami
?product_id=1;ls
?product_id=1;cat /etc/passwd
?product_id=1;pwd
?product_id=1 && cat /var/www/html/config.php
?product_id=$(whoami)
?product_id=`id`
```

### 3. Impact Analysis

* **Complete Server Compromise:** Attackers gain full command execution capabilities on the web server
* **Inventory Database Access:** Direct access to stock control systems and inventory databases
* **Financial Data Exposure:** Access to pricing, supplier information, and financial records
* **Supply Chain Disruption:** Ability to manipulate stock levels, creating operational chaos
* **Customer Data Breach:** Access to customer orders, payment information, and personal data
* **Confidentiality: CRITICAL** - Complete filesystem access, database credentials exposure, source code theft
* **Integrity: CRITICAL** - Ability to modify stock records, inject backdoors, alter application code
* **Availability: CRITICAL** - Can delete critical files, crash services, or launch DoS attacks
* **Compliance Violations:** PCI-DSS, GDPR, SOX violations due to data breach
* **Reputational Damage:** Loss of customer trust and business credibility

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED

This vulnerability poses an existential threat to the application and organization. The ease of exploitation combined with the catastrophic impact makes this a top-priority security issue requiring immediate remediation.

### 6. Remediation / Mitigation Steps

1. **Implement Strict Input Validation:**
   * Validate that product\_id contains only numeric characters using ctype\_digit() or similar functions
   * Type cast input to integer to ensure data type safety
   * Validate the product ID is within an acceptable range (e.g., 1-1000)
2. **Eliminate Shell Command Execution:**
   * Never use shell\_exec(), exec(), system(), or similar functions with user input
   * Access stock data directly from the database using prepared statements
   * Replace shell scripts with native application code
3. **Use Whitelist Validation:**
   * Define an array of allowed product IDs
   * Reject any product ID not in the whitelist
4. **Apply Defense in Depth:**
   * Run the web application with minimal system privileges
   * Implement Web Application Firewall (WAF) rules
   * Enable security logging and monitoring for suspicious patterns
   * Conduct regular security code reviews and penetration testing

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

* **Screenshot 1: Normal Functionality**
  * URL parameter: ?product\_id=1
  * Legitimate output: "Stock: 13 Pieces"
  * Demonstrates intended application behavior

<figure><img src="../../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

* **Screenshot 2: Successful Command Injection**
  * Modified URL: ?product\_id=id
  * Malicious output: `Stockuid=33(www-data) gid=33(www-data) groups=33(www-data)` Pieces
  * Confirms arbitrary command execution with www-data privileges
  * Demonstrates complete lack of input validation
