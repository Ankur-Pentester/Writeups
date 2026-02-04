# Invoices

## Vulnerability Report : Insecure Direct Object Reference (IDOR)

**Challenge Name:** Invoices

### 1. Vulnerability Name & Description

**Insecure Direct Object Reference (IDOR)**

Insecure Direct Object Reference (IDOR) is an access control vulnerability that occurs when an application exposes direct references to internal implementation objects (such as database keys, filenames, or user IDs) without proper authorization checks. Attackers can manipulate these references to access unauthorized data belonging to other users. The vulnerability exists when the application uses user-supplied input to directly access objects in the backend without verifying that the requesting user has permission to access the requested resource. This allows attackers to enumerate and access sensitive information by simply changing parameter values like invoice\_id=1, invoice\_id=2, invoice\_id=3, etc., bypassing intended access controls and viewing data belonging to other users.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/idor/invoices/index.php](http://localhost:1337/lab/idor/invoices/index.php)

**Vulnerable Parameter:** invoice\_id (GET parameter)

**Normal Functionality:**

1. Access Invoices page at [http://localhost:1337/lab/idor/invoices/](http://localhost:1337/lab/idor/invoices/)
2. Page displays message: "You have a new invoice!"
3. Page shows: "Click to view your invoice!"
4. Click "View" button to access invoice

**Initial Invoice Access:**

1. Click "View" button
2. Navigate to: `?invoice_id=1`
3. Invoice PDF displays for "Sean Johnson"
4. Shows Invoice Number: 00001
5. Contains billing information, company details, and line items
6. Represents legitimate access to assigned invoice

**IDOR Exploitation:**

**Attack Steps:**

1. Observe URL parameter: `?invoice_id=1`
2. Identify sequential numeric identifier used for invoice access
3. No authorization check validates user owns the requested invoice
4. Manually modify invoice\_id parameter to access other invoices
5. Change URL to: `?invoice_id=2`
6. Invoice PDF displays for "Elias Fetter"
7. Successfully accessed another user's invoice without authorization
8. Change URL to: `?invoice_id=3`
9. Invoice PDF displays for "Susan Fore"
10. Successfully enumerated and accessed multiple users' invoices
11. Confirms IDOR vulnerability allowing unauthorized data access

**IDOR Enumeration Payloads:**

\`# Sequential enumeration ?invoice\_id=1 ?invoice\_id=2 ?invoice\_id=3 ?invoice\_id=4 ?invoice\_id=5

## Range enumeration

`?invoice_id=1-100 ?invoice_id=1000-2000`

## Negative values

`?invoice_id=-1 ?invoice_id=0`

## Large numbers

`?invoice_id=999999 ?invoice_id=9999999999`

## Non-numeric attempts

`` ?invoice_id=admin ?invoice_id=all` ``

### 3. Impact Analysis

* **Unauthorized Data Access:** Complete access to all users' invoices and financial information
* **Privacy Violation:** Exposure of personal information including names, addresses, phone numbers, email addresses
* **Financial Information Disclosure:** Access to pricing, billing details, transaction history, payment information
* **Business Intelligence Leakage:** Competitors can view customer lists, pricing strategies, and business relationships
* **Customer Enumeration:** Ability to identify all customers and their transaction patterns
* **Compliance Violations:** GDPR, PCI-DSS, CCPA violations due to unauthorized access to personal and financial data
* **Competitive Disadvantage:** Business competitors gain access to confidential customer and pricing data
* **Reputational Damage:** Loss of customer trust when data breaches are discovered
* **Confidentiality: HIGH** - Complete access to all invoice data across entire user base
* **Integrity: LOW** - Read-only access, no direct modification capability
* **Availability: LOW** - Does not affect system availability
* **Legal Liability:** Potential lawsuits from affected customers and regulatory fines
* **Business Impact:** Customer churn, competitive disadvantage, regulatory penalties, brand damage

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 6.5 (Medium)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N`

### 5. Risk Rating / Severity

**SEVERITY: HIGH**

**Risk Level:** URGENT REMEDIATION REQUIRED

IDOR vulnerabilities expose sensitive user data and represent fundamental access control failures. This requires urgent remediation to prevent unauthorized access to confidential financial and personal information.

### 6. Remediation / Mitigation Steps

1. **Implement Proper Authorization Checks:**
   * Verify user owns requested resource before allowing access
   * Check user session against resource ownership in database
   * Deny access if user is not authorized for specific resource
   * Never rely solely on hiding or obfuscating object references
2. **Use Indirect Reference Maps:**
   * Replace direct database IDs with temporary random tokens
   * Create per-user session mapping between tokens and actual IDs
   * Generate new tokens for each session
   * Validate token belongs to current user's session
3. **Implement Access Control Lists (ACL):**
   * Maintain database table mapping users to accessible resources
   * Query ACL before granting access to any resource
   * Implement role-based access control (RBAC) where appropriate
   * Log all access attempts for audit purposes
4. **Use Non-Sequential Identifiers:**
   * Replace sequential IDs with UUIDs or random tokens
   * Makes enumeration more difficult (not a complete solution)
   * Still requires proper authorization checks
   * Consider this defense-in-depth measure only
5. **Rate Limiting and Monitoring:**
   * Implement rate limiting on sensitive endpoints
   * Monitor for suspicious enumeration patterns
   * Alert on excessive sequential access attempts
   * Implement CAPTCHA for repeated access attempts
6. **Server-Side Enforcement:**
   * Always enforce authorization server-side
   * Never rely on client-side security controls
   * Validate every request regardless of referrer
   * Implement consistent authorization logic across all endpoints

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

Screenshot 1: Invoices page displays "You have a new invoice!" message with "View" button indicating invoice viewing functionality at [http://localhost:1337/lab/idor/invoices/](http://localhost:1337/lab/idor/invoices/).

<figure><img src="../../../.gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>

Screenshot 2: Clicking View with URL parameter ?invoice\_id=1 displays invoice PDF for "Sean Johnson" with Invoice Number 00001, showing legitimate user's financial document.

<figure><img src="../../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

Screenshot 3: Modifying URL to ?invoice\_id=2 displays invoice PDF for "Elias Fetter" demonstrating unauthorized access to another user's invoice through IDOR.

<figure><img src="../../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

Screenshot 4: Further enumeration with ?invoice\_id=3 displays invoice PDF for "Susan Fore" confirming ability to access multiple users' sensitive financial documents by simply incrementing ID parameter without authorization checks.
