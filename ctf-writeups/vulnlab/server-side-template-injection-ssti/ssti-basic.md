# SSTI-Basic

## Vulnerability Report : Server-Side Template Injection (SSTI)

**Challenge Name:** SSTI-Basic

### 1. Vulnerability Name & Description

**Server-Side Template Injection (SSTI) - Basic**

Server-Side Template Injection occurs when user input is embedded into template engines without proper sanitization, allowing attackers to inject template directives that are executed on the server. Template engines like Twig (PHP), Jinja2 (Python), Smarty, or others process these directives server-side, enabling attackers to execute arbitrary code, read sensitive files, and compromise the entire server. Unlike client-side template injection, SSTI executes on the server with full application privileges, making it extremely dangerous. The vulnerability exists when user-controlled data is directly concatenated into templates rather than being passed as safe template variables.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/ssti/SSTI-Basic/](http://localhost:1337/lab/ssti/SSTI-Basic/)

**Vulnerable Parameter:** Blog post text input field

**Template Engine Detected:** PHP (based on Wappalyzer showing PHP as programming language)

**Initial Discovery:**

* Application displays "Welcome to Blog" with blog post functionality
* User input is processed through a template engine
* Wappalyzer confirms PHP backend

**Detection Payload:**

`{{7*7}}`

**Attack Steps:**

1. Access SSTI-Basic page at [http://localhost:1337/lab/ssti/SSTI-Basic/](http://localhost:1337/lab/ssti/SSTI-Basic/)
2. Enter mathematical expression in blog post field: `{{7*7}}`
3. Click "Submit" button
4. Observe output in "Your blog posts" section displays: `49`
5. Confirms template engine evaluates expressions (7\*7=49)
6. Template injection vulnerability confirmed

**Command Execution Payload:**

`{{ ['id'] | filter('system') }}`

**Command Execution Steps:**

1. Enter SSTI payload for command execution: `{{ ['id'] | filter('system') }}`
2. Submit the malicious template injection
3. Application processes the filter function with 'system' parameter
4. Output displays: `uid=33(www-data) gid=33(www-data) groups=33(www-data) Array`
5. Successfully executed `id` command on server with www-data privileges

**Alternative Payload for Better Output:**

`{{ ['whoami'] | filter('system') }}`

1. Enter refined payload: `{{ ['whoami'] | filter('system') }}`
2. Submit and observe cleaner output: `www-data Array`
3. Confirms full Remote Code Execution capability

### 3. Impact Analysis

* **Remote Code Execution (RCE):** Attackers can execute arbitrary system commands with web server privileges
* **Complete Server Compromise:** Full control over the application server and underlying operating system
* **Sensitive File Disclosure:** Read configuration files, database credentials, source code, and system files
* **Data Exfiltration:** Access to all application data, user information, and business-critical data
* **Privilege Escalation:** Potential to escalate from www-data to root privileges
* **Lateral Movement:** Compromised server can be used as pivot point to attack internal network
* **Backdoor Installation:** Persistent access through web shells or system-level backdoors
* **Database Compromise:** Direct access to database through stolen credentials
* **Confidentiality: CRITICAL** - Complete access to all server files and data
* **Integrity: CRITICAL** - Ability to modify any file, inject malware, and alter application behavior
* **Availability: CRITICAL** - Can crash services, delete files, or launch DoS attacks
* **Business Impact:** Complete application compromise, data breach, reputational damage, regulatory violations

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED - EMERGENCY

Server-Side Template Injection with RCE is one of the most severe web application vulnerabilities. It provides attackers with complete control over the server and should be treated as a critical security emergency requiring immediate remediation.

### 6. Remediation / Mitigation Steps

1. **Never Use User Input in Templates:**
   * Never concatenate user input directly into template strings
   * Always pass user data as template variables, not as template code
   * Use template engine's safe parameter passing mechanisms
2. **Use Sandboxed Template Environments:**
   * Enable template engine's sandbox mode if available
   * Disable dangerous functions and filters (system, exec, passthru, eval)
   * Restrict access to sensitive objects and methods
3. **Input Validation and Sanitization:**
   * Validate all user input against strict whitelist patterns
   * Reject input containing template syntax characters like `{{, }}, {%, %}`
   * Implement Content Security Policy (CSP) headers
4. **Use Logic-less Templates:**
   * Consider using logic-less template engines like Mustache
   * Separate business logic from presentation layer completely
   * Avoid complex template expressions
5. **Apply Security Updates:**
   * Keep template engines updated to latest versions
   * Monitor security advisories for template engine vulnerabilities
   * Implement Web Application Firewall (WAF) rules to detect SSTI attempts

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Blog page with Wappalyzer showing PHP backend, initial test with mathematical expression `{{7*7}}` displays result `49` in blog posts confirming template engine evaluates expressions and SSTI vulnerability exists.

<figure><img src="../../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: SSTI exploitation with payload `{{ ['id'] | filter('system') }}` displays `uid=33(www-data) gid=33(www-data) groups=33(www-data)` Array confirming successful Remote Code Execution on server.

<figure><img src="../../../.gitbook/assets/image (199).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Refined payload `{{ ['whoami'] | filter('system') }}` displays cleaner output www-data Array confirming complete server compromise with command execution capabilities.
