# User Agent

### Vulnerability #6: Stored Cross-Site Scripting via User-Agent Header

Challenge Name: User Agent

#### 1. Vulnerability Name & Description

Stored Cross-Site Scripting (XSS) via User-Agent HTTP Header

The application logs and stores the User-Agent HTTP header from client requests without sanitization. When this data is displayed on the admin panel page, the stored malicious script executes in the browser of anyone viewing the logs. This allows attackers to inject JavaScript by manipulating their User-Agent header.

#### 2. Proof of Concept (PoC)

Vulnerable Endpoint: [http://localhost:1337/lab/xss/user-agent/](http://localhost:1337/lab/xss/user-agent/)

Vulnerable Header: User-Agent

Payload Used:

`<script>alert(9)</script>`

Attack Steps:

1. Access the User Agent challenge and login with credentials mandalorian/mandalorian
2. After successful login, message displays: "Admin logs your user agent data. if you are curious about how it looks from admin panel"
3. Click "Click Here" button to view stored user agent logs
4. Table shows username and corresponding User-Agent strings from previous requests
5. Intercept the POST request using Burp Suite
6. Modify User-Agent header to: \<script>alert(9)\</script>
7. Forward the modified request
8. Payload is stored in database without sanitization
9. When admin panel page loads, the stored script executes automatically
10. Alert box displays "localhost"

HTTP Request with Malicious User-Agent:

`POST /lab/xss/user-agent/user_agent_stored.php HTTP/1.1 Host: localhost:1337 User-Agent: <script>alert(9)</script>`

#### 3. Impact Analysis

* Persistent XSS affecting all users viewing the admin panel
* Session hijacking of admin accounts through cookie theft
* Account takeover of privileged users
* Malware distribution and phishing attacks
* Keylogging and credential harvesting from admin users
* Complete compromise of admin functionality
* Difficult to detect as attack vector is in HTTP headers
* Can bypass traditional input validation on forms

#### 4. CVSS 3.1 Score & Vector String

CVSS Base Score: 7.1 (High)

Vector String: `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:L/I:L/A:L`

#### 5. Risk Rating / Severity

Severity Level: HIGH

This vulnerability is particularly dangerous as it targets admin users and persists in the database, automatically executing whenever logs are viewed.

#### 6. Remediation / Mitigation Steps

1. Sanitize and encode all HTTP headers before storing:

\`_// Insecure_ $userAgent = $\_SERVER\['HTTP\_USER\_AGENT']; $stmt->execute(\[$username, $userAgent]);

_// Secure_ $userAgent = htmlspecialchars($\_SERVER\['HTTP\_USER\_AGENT'], ENT\_QUOTES, 'UTF-8'); $stmt->execute(\[$username, $userAgent]);\`

1. Apply output encoding when displaying stored data:

`echo htmlspecialchars($row['user_agent'], ENT_QUOTES, 'UTF-8');`

1. Implement Content Security Policy headers to prevent inline script execution
2. Validate User-Agent strings - reject those containing HTML/JavaScript syntax
3. Use prepared statements to prevent SQL injection combined with XSS
4. Limit User-Agent string length to reasonable values (e.g., 255 characters)
5. Implement input validation on server-side for all HTTP headers

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (350).png" alt=""><figcaption></figcaption></figure>

* Figure 1: Login page with mandalorian/mandalorian credentials displayed

<figure><img src="../../../.gitbook/assets/image (351).png" alt=""><figcaption></figcaption></figure>

* Figure 2: Success message after login: "Admin logs your user agent data. if you are curious about how it looks from admin panel" with "Click Here" button

<figure><img src="../../../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>

* Figure 3: Admin panel displaying table of stored User-Agent data showing various browser user agents from different users

<figure><img src="../../../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>

* Figure 4: Burp Suite intercept showing POST request with malicious User-Agent header:`<script>alert(9)</script>`

<figure><img src="../../../.gitbook/assets/image (354).png" alt=""><figcaption></figcaption></figure>

* Figure 5: Burp Suite menu showing "Do intercept" and "Response to this request" options for forwarding the modified request

<figure><img src="../../../.gitbook/assets/image (355).png" alt=""><figcaption></figcaption></figure>

* Figure 6: Alert box displaying "localhost" confirming successful stored XSS exploitation via User-Agent header

