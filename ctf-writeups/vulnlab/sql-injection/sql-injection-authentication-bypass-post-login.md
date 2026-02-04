# SQL Injection – Authentication Bypass (Post Login)

### Vulnerability : SQL Injection – Authentication Bypass (Post Login)

***

#### 1. Vulnerability Name & Description

**Challenge Name:** Automatic Login – Post Login

**Vulnerability Type:** SQL Injection (Authentication Bypass)

The login functionality of the application fails to properly sanitize user-supplied input in the username field. Due to insecure query construction, an attacker can inject SQL logic that always evaluates to true, resulting in authentication bypass without knowing valid credentials.

This vulnerability allows direct access to the administrator panel by manipulating the SQL query logic.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/sql-injection/post-login/>
```

**Vulnerable Parameters:**

* Username (POST)
* Password (POST)

***

#### Step 1: Inject Malicious SQL Payload

**Payload used in Username field:**

```
' or 1=1#
```

**Password Field:**

```
(any value)
```

***

#### Step 2: Submit Login Request

After clicking **System Login**, the backend SQL query becomes logically true due to injected condition (`OR 1=1`), and the authentication check is bypassed.

***

#### Step 3: Successful Authentication Bypass

The application automatically redirects to the admin dashboard without validating legitimate credentials.

**Admin Panel Accessed:**

```
<http://localhost:1337/lab/sql-injection/post-login/admin.php>
```

***

#### 3. Impact Analysis

* Complete authentication bypass
* Unauthorized admin panel access
* No valid credentials required
* Exposure of sensitive administrator information
* Potential modification or deletion of critical data
* Full application compromise possible

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.1 (Critical)

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N
```

***

#### 5. Risk Rating / Severity

**Severity Level:** **CRITICAL**

This vulnerability allows unauthenticated attackers to gain administrator-level access with a single crafted payload.

***

#### 6. Remediation / Mitigation Steps

1. Use prepared statements / parameterized queries
2. `$stmt = $pdo>prepare("SELECT * FROM users WHERE username = ? AND password = ?"); $stmt>execute([$username, $password]);`
3. Never concatenate user input into SQL queries
4. Implement server-side input validation
5. Use secure password hashing (bcrypt / Argon2)
6. Apply account lockout mechanisms
7. Disable detailed SQL errors in production
8. Implement logging and alerting for failed login attempts

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

* Login page showing SQL injection payload `(' or 1=1#)`
* Successful login without valid credentials

<figure><img src="../../../.gitbook/assets/image (271).png" alt=""><figcaption></figcaption></figure>

* Admin dashboard access confirmation
* URL redirection to `admin.php`

