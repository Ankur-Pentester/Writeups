# Send Ping

### Vulnerability : OS Command Injection - Basic

Challenge Name: Send Ping

#### 1. Vulnerability Name & Description

OS Command Injection via Ping Functionality

The application accepts user input to execute a ping command on the server without proper input validation or sanitization. Attackers can inject additional operating system commands using command separators (such as ;, &&, ||, |) to execute arbitrary commands on the underlying server with the privileges of the web application.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/command-injection/ping-low/](http://localhost:1337/lab/command-injection/ping-low/)

Vulnerable Parameter: IP address input field

Payload Used:

`;id`

Attack Steps:

1. Access the Send Ping challenge page
2. Input field labeled "PING" with text box for entering target
3. Enter ";id" in the input field
4. Click "Ping" button
5. Application executes: ping ;id
6. Command separator (;) terminates ping command and executes id command
7. Output displays: uid=33(www-data) gid=33(www-data) groups=33(www-data)
8. Reveals web server is running as www-data user

Backend Command Execution:

```bash
ping ;id
*# Executes as: ping [target] ; id*

Alternative Payloads:

;ls
;cat /etc/passwd
;whoami
&& id
| id
|| id
```

#### 3. Impact Analysis

* Complete server compromise through arbitrary command execution
* Data exfiltration from server filesystem
* Password and credential theft from configuration files
* Privilege escalation attempts
* Malware installation and backdoor creation
* Denial of Service by executing resource-intensive commands
* Lateral movement within internal network
* Database compromise if credentials accessible
* Server can be used for attacking other systems
* Complete loss of confidentiality, integrity, and availability

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 9.8 (Critical)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

Metrics Breakdown:

* Attack Vector (AV:N): Network - remotely exploitable
* Attack Complexity (AC:L): Low - no special conditions required
* Privileges Required (PR:N): None - no authentication needed
* User Interaction (UI:N): None - automatic exploitation
* Scope (S:U): Unchanged - impacts only the vulnerable component
* Confidentiality (C:H): High - complete information disclosure
* Integrity (I:H): High - complete data modification possible
* Availability (A:H): High - complete system disruption possible

#### 5. Risk Rating / Severity

Severity Level: CRITICAL

OS Command Injection is one of the most severe vulnerabilities as it allows direct execution of system commands, leading to complete server compromise.

#### 6. Remediation / Mitigation Steps

1. Avoid executing system commands from user input entirely - use native language functions instead:

\`_// Insecure_ $target = $\_POST\['target']; $output = shell\_exec("ping -c 4 " . $target);

_// Secure - avoid shell execution// Use native PHP socket functions or ICMP libraries instead_\`

1. If system commands are absolutely necessary, use parameterized execution:

`*// Use escapeshellarg() and escapeshellcmd()* $target = escapeshellarg($_POST['target']); $output = shell_exec("ping -c 4 " . $target);`

1. Implement strict input validation with whitelist approach:

`$target = $_POST['target']; if (filter_var($target, FILTER_VALIDATE_IP)) { *// Valid IP address* $target = escapeshellarg($target); $output = shell_exec("ping -c 4 " . $target); } else { die("Invalid IP address"); }`

4. Use application-level restrictions and sandboxing
5. Run web application with minimal privileges (principle of least privilege)
6. Disable dangerous PHP functions in php.ini:

`disable_functions = exec,passthru,shell_exec,system,proc_open,popen`

1. Implement Web Application Firewall (WAF) rules to detect command injection patterns
2. Regular security audits and penetration testing

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Command execution result displaying system information: `uid=33(www-data) gid=33(www-data) groups=33(www-data)`, confirming successful command injection and revealing the web server process runs as www-data user
