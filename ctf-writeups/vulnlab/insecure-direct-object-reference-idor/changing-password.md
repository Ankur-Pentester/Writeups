# Changing Password

## Vulnerability Report : Insecure Direct Object Reference (IDOR) - Password Change

**Challenge Name:** Changing Password

### 1. Vulnerability Name & Description

**Insecure Direct Object Reference (IDOR) in Password Change Functionality**

This IDOR vulnerability exists in the password change functionality where the application fails to validate that the authenticated user has authorization to change the password for the specified user account. The vulnerability occurs because the application uses a client-controlled user\_id parameter to determine which account's password should be changed, without verifying that the currently authenticated user (Christopher) has permission to modify that account. By manipulating the user\_id parameter in the password change request, attackers can change passwords for any user account in the system, leading to complete account takeover. This represents a critical authorization bypass allowing horizontal privilege escalation to access other users' accounts.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/idor/changing-password/index.php](http://localhost:1337/lab/idor/changing-password/index.php)

**Vulnerable Parameter:** user\_id (POST parameter)

**Normal Functionality:**

1. Access Changing Password page at [http://localhost:1337/lab/idor/changing-password/](http://localhost:1337/lab/idor/changing-password/)
2. Page displays current logged-in user: "Your username: Christopher"
3. Password change form shows: "Password Setting"
4. Form contains field: "Enter your new password"
5. User enters new password and clicks "Confirm"
6. Success message displays: "Password change successful!"
7. Confirmation shows: "Christopher's password has been changed"

**IDOR Exploitation:**

**`Attack Steps:`**

1. Enable FoxyProxy browser extension to route traffic through Burp Suite
2. Click "Time" option in FoxyProxy to activate proxy
3. Enter new password "Hacker" in password field
4. Click "Confirm" button to submit password change request
5. Burp Suite Proxy intercepts POST request to /lab/idor/changing-password/index.php
6. Examine request body showing parameters: `password=Hacker&user_id=1`
7. Identify user\_id parameter controls target account for password change
8. Current user Christopher has user\_id=1
9. Right-click intercepted request, select "Do intercept" and "Response to this request"
10. Modify user\_id parameter from 1 to 2: `password=Hacker&user_id=2`
11. Forward modified request to server
12. Server accepts modified user\_id without authorization check
13. Response shows: "Password change successful!"
14. Success message displays: "Pierre's password has been changed"
15. Successfully changed password for user "Pierre" (user\_id=2)
16. Can now login as Pierre using new password "Hacker"
17. Achieved complete account takeover through IDOR

### 3. Impact Analysis

* **Complete Account Takeover:** Attackers can gain full control of any user account by changing their password
* **Mass Account Compromise:** Can enumerate and compromise all user accounts in the system
* **Administrative Access:** Potential to compromise admin accounts and gain elevated privileges
* **Data Breach:** Access to all data belonging to compromised accounts
* **Identity Theft:** Ability to impersonate any user in the system
* **Service Disruption:** Legitimate users locked out of their accounts after password changes
* **Persistent Access:** Password changes provide long-term unauthorized access
* **Privilege Escalation:** Horizontal escalation to other users, potential vertical escalation to admins
* **Confidentiality: CRITICAL** - Complete access to all user accounts and associated data
* **Integrity: CRITICAL** - Ability to modify account credentials and perform actions as other users
* **Availability: HIGH** - Can lock legitimate users out of their accounts
* **Business Impact:** Complete system compromise, massive data breach, loss of user trust, regulatory violations
* **Legal Liability:** Severe consequences for failing to protect user credentials and accounts

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.1 (Critical)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:H/I:H/A:L`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED - EMERGENCY

IDOR vulnerabilities in password change functionality represent critical security failures enabling complete account takeover. This requires immediate emergency remediation to prevent widespread account compromise.

### 6. Remediation / Mitigation Steps

1. **Implement Proper Authorization Checks:**
   * Always verify the authenticated user has permission to change the target account's password
   * Compare session user ID against target user ID before allowing changes
   * Never rely solely on client-provided user identifiers
2. **Remove User ID from Client Control:**
   * Never accept user\_id from client requests
   * Always use session-based user identification
   * Retrieve user ID exclusively from authenticated session
3. **Require Current Password:**
   * Mandate current password before allowing password change
   * Adds additional authentication layer
   * Prevents unauthorized changes even if session is compromised
4. **Implement Rate Limiting:**
   * Limit password change attempts per user per time period
   * Detect and block suspicious patterns of mass password changes
   * Alert security team on anomalous activity
5. **Additional Security Measures:**
   * Send email notifications for password changes
   * Implement multi-factor authentication
   * Log all password change attempts with timestamps and IP addresses
   * Invalidate all existing sessions after password change
   * Enforce strong password policies
6. **Security Monitoring:**
   * Alert on rapid password changes across multiple accounts
   * Monitor for parameter tampering attempts
   * Implement anomaly detection for unusual access patterns

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (307).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Password change page displays logged-in user "Christopher" with password change form showing successful password change for Christopher's own account.

<figure><img src="../../../.gitbook/assets/image (308).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: After successful password change, success message confirms "Christopher's password has been changed" demonstrating normal functionality for legitimate user.

<figure><img src="../../../.gitbook/assets/image (309).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: FoxyProxy extension enabled with "Time" option selected to route traffic through Burp Suite, password field contains "Hacker", preparing to intercept request.

<figure><img src="../../../.gitbook/assets/image (310).png" alt=""><figcaption></figcaption></figure>

* Screenshot 4: Burp Suite intercepts POST request showing parameters `password=Hacker&user_id=1`, with context menu displaying "Do intercept" and "Response to this request" options for request manipulation.

<figure><img src="../../../.gitbook/assets/image (311).png" alt=""><figcaption></figcaption></figure>

* Screenshot 5: Modified request with `password=Hacker&user_id=2` targeting different user, context menu shows interception options to forward tampered request.

<figure><img src="../../../.gitbook/assets/image (312).png" alt=""><figcaption></figcaption></figure>

* Screenshot 6: Success message displays "Pierre's password has been changed" confirming IDOR exploitation successfully changed another user's password without authorization, achieving complete account takeover.
