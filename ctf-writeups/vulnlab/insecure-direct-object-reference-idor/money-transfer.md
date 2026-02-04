# Money Transfer

## Vulnerability Report : IDOR - Unauthorized Money Transfer via Parameter Tampering

**Challenge Name:** Money Transfer

### 1. Vulnerability Name & Description

**Insecure Direct Object Reference (IDOR) with Parameter Tampering in Money Transfer**

This vulnerability combines IDOR with parameter tampering in a financial transaction system. The application fails to validate that the authenticated user (sender) is authorized to initiate transfers from the specified sender account. By manipulating the sender\_id parameter, attackers can transfer money from other users' accounts without their knowledge or authorization. The vulnerability exists because the application uses client-controlled parameters to determine both the sender and recipient of transfers, without verifying that the authenticated session user matches the sender\_id. This allows attackers to drain funds from any user account in the system by simply changing a hidden parameter value, representing a critical financial fraud vulnerability.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/idor/money-transfer/index.php](http://localhost:1337/lab/idor/money-transfer/index.php)

**Vulnerable Parameters:** transfer\_amount, recipient\_id, sender\_id (POST parameters)

**Normal Functionality:**

1. Access Money Transfer page at [http://localhost:1337/lab/idor/money-transfer/index.php?message=success](http://localhost:1337/lab/idor/money-transfer/index.php?message=success)
2. Page displays current user account: "Your account name: User 1"
3. Account balance shows: "Your money in your account: 990 $"
4. Previous transaction displays: "The money transfer was successful!"
5. Shows transfer details: "Transfer amount: 1" to "Receiver ID: 2"
6. User table shows: User 1 has 990 $, User 2 has 1010 $
7. User can enter transfer amount and recipient ID

**IDOR with Parameter Tampering Exploitation:**

**Attack Steps:**

1. Enter transfer amount: 1 and recipient ID: 2
2. Intercept transfer request using Burp Suite Proxy
3. Observe POST request to /lab/idor/money-transfer/index.php
4. Request body shows parameters: `transfer_amount=1&recipient_id=2&sender_id=1`
5. Identify sender\_id parameter controls which account funds are deducted from
6. Current authenticated user is User 1 (sender\_id=1)
7. Modify parameters to transfer from different user's account
8. Change to: `transfer_amount=500&recipient_id=1&sender_id=2`
9. This transfers 500 from User 2's account to User 1's account
10. Right-click request, select "Do intercept" and "Response to this request"
11. Forward manipulated request to server
12. Server processes transfer without validating sender authorization

**Exploitation Result:**

1. Success message displays: "The money transfer was successful!"
2. User 1's balance increases to: "Your money in your account: 1490 $"
3. User table shows updated balances:
   * User 1: 1490 $ (gained 500 $)
   * User 2: 510 $ (lost 500 $)
4. Successfully transferred money FROM User 2 TO User 1
5. User 2 had no knowledge or control over this transaction
6. Attacker (User 1) gained financial benefit by stealing from User 2

### 3. Impact Analysis

* **Financial Fraud:** Direct theft of money from user accounts without authorization
* **Unauthorized Fund Transfers:** Ability to transfer money from any account to any account
* **Account Draining:** Can completely empty victim accounts by transferring all funds
* **Mass Financial Theft:** Can systematically drain multiple accounts to attacker's account
* **Business Logic Bypass:** Complete circumvention of legitimate transfer authorization
* **Fraudulent Transaction Records:** Creates false transaction history for victims
* **Money Laundering:** Can move money through multiple accounts to obscure theft
* **Victim Financial Loss:** Users lose money without initiating any transactions
* **Trust Destruction:** Complete loss of customer confidence in platform security
* **Confidentiality: LOW** - No direct data disclosure beyond account balances
* **Integrity: CRITICAL** - Complete compromise of financial transaction integrity
* **Availability: MEDIUM** - Drained accounts prevent legitimate users from conducting business
* **Financial Impact:** Unlimited monetary loss through systematic account theft
* **Legal Liability:** Criminal fraud, regulatory violations, class action lawsuits
* **Business Viability:** Platform becomes insolvent if exploited at scale
* **Regulatory Consequences:** Severe penalties for failing to protect financial transactions

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.1 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:L/I:H/A:L`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED - EMERGENCY

Financial transaction vulnerabilities allowing unauthorized fund transfers represent existential threats to any financial platform. This requires immediate emergency shutdown and remediation.

### 6. Remediation / Mitigation Steps

1. **Implement Strict Authorization Checks:**
   * Always verify authenticated user matches sender account
   * Never accept sender\_id from client requests
   * Retrieve sender from authenticated session only

`` `session_start(); $authenticated_user_id = $_SESSION['user_id']; $transfer_amount = (int)$_POST['transfer_amount']; $recipient_id = (int)$_POST['recipient_id']; ``

`// CRITICAL: Never use sender_id from client // Always use authenticated session user as sender $sender_id = $authenticated_user_id;`

`// Verify sender has sufficient funds if (!hasSufficientBalance($sender_id, $transfer_amount)) { die("Insufficient funds"); }`

`` // Process transfer with verified sender processTransfer($sender_id, $recipient_id, $transfer_amount);` ``

1. **Remove Client-Controlled Sender Parameter:**
   * Completely eliminate sender\_id from client requests
   * Sender must always be determined server-side from session
   * No way for client to specify sender account
2. **Implement Transaction Validation:**
   * Verify sender account exists and is active
   * Check recipient account exists and can receive funds
   * Validate transfer amount is positive and within limits
   * Enforce minimum/maximum transaction limits

`` `// Comprehensive validation if ($transfer_amount <= 0) { die("Invalid transfer amount"); } ``

`if ($transfer_amount > MAX_TRANSFER_LIMIT) { die("Exceeds maximum transfer limit"); }`

`if ($sender_id === $recipient_id) { die("Cannot transfer to same account"); }`

`` if (!accountExists($recipient_id)) { die("Recipient account not found"); }` ``

1. **Implement Transaction Verification:**
   * Require two-factor authentication for transfers
   * Send confirmation emails for all transfers
   * Implement transaction approval workflow
   * Add delay before processing large transfers
2. **Additional Security Measures:**
   * Use database transactions with proper locking
   * Implement transfer rate limiting per user
   * Monitor for suspicious transfer patterns
   * Log all transfer attempts with full audit trail
   * Alert on rapid consecutive transfers
   * Implement daily transfer limits
3. **Fraud Detection:**
   * Detect unusual transfer patterns (amount, frequency, recipients)
   * Alert on transfers from accounts that haven't been accessed recently
   * Flag transactions where sender differs from authenticated user
   * Implement machine learning anomaly detection

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (313).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Money Transfer page shows User 1 with balance 990 $ and successful transfer of 1 $ to User 2, with user table displaying User 1: 990 $ and User 2: 1010 $ demonstrating normal transaction flow.

<figure><img src="../../../.gitbook/assets/image (314).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Burp Suite intercepts POST request revealing vulnerable parameters `transfer_amount=1&recipient_id=2&sender_id=1` showing client controls sender identification enabling unauthorized transfers.

<figure><img src="../../../.gitbook/assets/image (315).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Modified request with parameters `transfer_amount=500&recipient_id=1&sender_id=2` with context menu showing "Do intercept" and "Response to this request" options preparing to execute fraudulent transfer from User 2 to User 1.

<figure><img src="../../../.gitbook/assets/image (316).png" alt=""><figcaption></figcaption></figure>

* Screenshot 4: After forwarding tampered request, page displays User 1's balance increased to 1490 $ with success message, user table shows User 1: 1490 $ and User 2: 510 $ confirming successful theft of 500 $ from User 2's account without authorization.
