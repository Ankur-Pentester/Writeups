# Address Entry

## Vulnerability Report : IDOR - Unauthorized Address and Order Modification

**Challenge Name:** Address Entry

### 1. Vulnerability Name & Description

**Insecure Direct Object Reference (IDOR) in Order and Address Management**

This vulnerability exists in the address and order management system where the application fails to validate that the authenticated user has authorization to view or modify addresses and orders. The application uses client-controlled addressID parameter to determine which addresses and orders to display or process, without verifying that the currently authenticated user has permission to access those resources. By manipulating the addressID parameter, attackers can view and modify other users' delivery addresses and order information.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/idor/address-entry/index.php](http://localhost:1337/lab/idor/address-entry/index.php)

**Vulnerable Parameters:** address, addressID, order (POST parameters)

**Attack Steps:**

1. Access Address Entry page showing user "Jesus S. Green" with address "Hey I am hacker"
2. Click "Order" button to place order
3. Success message displays order for "Jesus S. Green" with address "Hey I am hacker"
4. Intercept order request using Burp Suite showing parameters: `address=&addressID=1&order=`
5. Modify addressID parameter to: `address=&addressID=2&order=`
6. Forward modified request
7. Response displays different user's data: "Order address: 38740 McDermott Centers Suite 216 Keelingfurt, CO 79459-7315, Name: Kimberly J. Price"
8. Successfully accessed addressID=2 belonging to another user
9. Modify to `address=&addressID=3&order=` and forward request
10. Response shows third user: "Order address: 9287 Kacie View Apt. 466 Roycetown, RI 41148-8999, Name: Esperanza A. Donlon"
11. Confirmed ability to enumerate all user addresses in system

**Enumeration Payloads:**

`address=&addressID=1&order= address=&addressID=2&order= address=&addressID=3&order= address=&addressID=4&order=`

### 3. Impact Analysis

* **Privacy Violation:** Unauthorized access to users' personal delivery addresses
* **Package Theft:** Attackers can redirect orders to exposed addresses and intercept packages
* **Personal Information Disclosure:** Complete names and residential addresses exposed
* **Physical Security Risk:** Home addresses enable stalking and physical threats
* **Order Manipulation:** Can place orders using other users' addresses without authorization
* **Identity Theft:** Full names and addresses facilitate fraud
* **Confidentiality: HIGH** - Complete exposure of personal addresses and names
* **Integrity: HIGH** - Ability to modify order destinations
* **Availability: LOW** - No direct impact on system availability
* **Legal Liability:** GDPR violations, civil lawsuits for address exposure
* **Business Impact:** Loss of customer trust, reputational damage

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 7.1 (High)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:L/A:N`

### 5. Risk Rating / Severity

**SEVERITY: HIGH**

**Risk Level:** URGENT REMEDIATION REQUIRED

### 6. Remediation / Mitigation Steps

1. **Implement Proper Authorization Checks:**
   * Verify authenticated user owns the requested address
   * Check user session against address ownership before display
2. **Use Session-Based Address Retrieval:**
   * Retrieve user's addresses from session user ID only
   * Never accept addressID from client for critical operations
3. **Implement Address-User Relationship Validation:**
   * Maintain strict database relationship between users and addresses
   * Use JOIN queries to ensure address belongs to user
4. **Additional Security Measures:**
   * Log all address access attempts
   * Implement rate limiting on address queries
   * Send email notifications for address modifications

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (317).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: Address Entry page shows user "Jesus S. Green" with registered address "Hey I am hacker" and "Update Address" and "Order" buttons.

<figure><img src="../../../.gitbook/assets/image (318).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Success message shows "Order placed successfully!" with legitimate order details for "Jesus S. Green".

<figure><img src="../../../.gitbook/assets/image (319).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Burp Suite intercepts POST request showing parameters `address=&addressID=1&order=` demonstrating client control over address selection.

<figure><img src="../../../.gitbook/assets/image (320).png" alt=""><figcaption></figcaption></figure>

* Screenshot 4: Modified request `address=&addressID=2&order=` displays unauthorized address "38740 McDermott Centers Suite 216 Keelingfurt, CO 79459-7315" for "Kimberly J. Price".

<figure><img src="../../../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>

* Screenshot 5: Further enumeration address=\&addressID=3\&order= reveals "9287 Kacie View Apt. 466 Roycetown, RI 41148-8999" for "Esperanza A. Donlon" demonstrating full address enumeration capability.
