# About

## Vulnerability Report : IDOR - Unauthorized Profile Access and Modification

**Challenge Name:** About

### 1. Vulnerability Name & Description

**Insecure Direct Object Reference (IDOR) in User Profile Management**

This vulnerability exists in the user profile management system where the application fails to validate that the authenticated user has authorization to view or modify profile information for specific user accounts. The application uses a client-controlled userid parameter in cookies to determine which user profile to display and modify, without verifying that the currently authenticated user has permission to access that profile. By manipulating the userid cookie value, attackers can view and modify other users' profiles, including personal information such as name, job title, email, phone number, and address.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/idor/about/](http://localhost:1337/lab/idor/about/)

**Vulnerable Parameter:** userid (Cookie parameter)

**Attack Steps:**

1. Access About page at [http://localhost:1337/lab/idor/about/](http://localhost:1337/lab/idor/about/) with FoxyProxy enabled
2. Page displays profile with "Edit Profile" button
3. Click "Edit Profile" button
4. Profile editor displays user information with "Save" button
5. Intercept request using Burp Suite showing GET /lab/idor/about/
6. Request shows Cookie header containing `userid=3`
7. Right-click request and select "Send to Repeater"
8. In Repeater, observe userid=3 in Cookie header
9. Click "Send" button to view response
10. Response displays profile for userid=3 with job title "Senior Javascript Developer", about section "I am Hacker", email "[cedrickelly12@outlook.com](mailto:cedrickelly12@outlook.com)", phone "208-407-8643"
11. Modify Cookie header changing userid from 3 to 1: `userid=1`
12. Click "Send" button to forward modified request
13. Response displays different user's profile with same structure but potentially different data
14. Successfully accessed userid=1 profile belonging to different user
15. Can enumerate all user profiles by incrementing userid values
16. Can also modify profiles by intercepting POST requests to save endpoint

**Enumeration Payloads:**

`Cookie: userid=1 Cookie: userid=2 Cookie: userid=3 Cookie: userid=4 Cookie: userid=5`

### 3. Impact Analysis

* **Privacy Violation:** Unauthorized access to users' personal information including names, emails, phone numbers, addresses
* **Personal Information Disclosure:** Complete profile data exposed including contact details and biographical information
* **Profile Manipulation:** Attackers can modify other users' profiles without authorization
* **Identity Theft:** Access to full names, emails, and phone numbers facilitates fraud
* **Phishing and Social Engineering:** Exposed email addresses enable targeted phishing attacks
* **Harassment:** Phone numbers and addresses enable harassment and stalking
* **Confidentiality: HIGH** - Complete exposure of personal profile information
* **Integrity: HIGH** - Ability to modify other users' profile data
* **Availability: LOW** - No direct impact on system availability
* **Business Impact:** Loss of customer trust, reputational damage, regulatory violations
* **Legal Liability:** GDPR violations, privacy law violations

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 7.1 (High)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:L/A:N`

### 5. Risk Rating / Severity

**SEVERITY: HIGH**

**Risk Level:** URGENT REMEDIATION REQUIRED

### 6. Remediation / Mitigation Steps

1. **Implement Proper Session Management:**
   * Store user ID in server-side session only, not in cookies
   * Verify authenticated user matches requested profile before display
2. **Remove User ID from Client Control:**
   * Never use client-provided userid for profile access
   * Retrieve user ID exclusively from server-side session
3. **Implement Profile Access Controls:**
   * Verify user owns profile before displaying or modifying
   * Use database queries with user authentication checks
4. **Additional Security Measures:**
   * Use HttpOnly and Secure flags on cookies
   * Implement CSRF tokens for profile modifications
   * Log all profile access attempts
   * Send email notifications for profile changes

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (322).png" alt=""><figcaption></figcaption></figure>

* Screenshot 1: About page displays user profile with "Edit Profile" button showing FoxyProxy extension enabled in browser at [http://localhost:1337/lab/idor/about/](http://localhost:1337/lab/idor/about/).

<figure><img src="../../../.gitbook/assets/image (323).png" alt=""><figcaption></figcaption></figure>

* Screenshot 2: Burp Suite HTTP History shows GET request to /lab/idor/about/ with Cookie header containing userid=3 parameter, context menu displays "Send to Repeater" option highlighted.

<figure><img src="../../../.gitbook/assets/image (324).png" alt=""><figcaption></figcaption></figure>

* Screenshot 3: Burp Repeater displays response for userid=3 showing complete profile with job title "Senior Javascript Developer", about section "I am Hacker", email "[cedrickelly12@outlook.com](mailto:cedrickelly12@outlook.com)", phone "208-407-8643", address field, with "Send" button highlighted at top.

<figure><img src="../../../.gitbook/assets/image (325).png" alt=""><figcaption></figcaption></figure>

* Screenshot 4: Modified Cookie header showing userid=1 in Burp Repeater request, demonstrating parameter manipulation to access different user's profile.

<figure><img src="../../../.gitbook/assets/image (326).png" alt=""><figcaption></figcaption></figure>

* Screenshot 5: Browser displays profile edit page for "Cedric Kelly" with complete contact information including email "[cedrickelly12@outlook.com](mailto:cedrickelly12@outlook.com)", phone "208-407-8643", address "Edinburgh", and "Save" button at bottom, showing successful profile access.

<figure><img src="../../../.gitbook/assets/image (327).png" alt=""><figcaption></figcaption></figure>

* Screenshot 6: Burp Suite intercepts POST request to /lab/idor/about/saveprofile.php showing profile update parameters in request body with "Send to Repeater" option in context menu.

<figure><img src="../../../.gitbook/assets/image (328).png" alt=""><figcaption></figcaption></figure>

* Screenshot 7: Burp Repeater shows POST request body containing profile update parameters with `puserid=3&pjob=Senior+Javascript+Developer&pabout=I+am+Hacker&pemail=cedrickelly12@40outlook.com&pphone=208-407-8643&plocation=Edinburgh,` with response showing HTTP 302 redirect status indicating successful save operation.

<figure><img src="../../../.gitbook/assets/image (329).png" alt=""><figcaption></figcaption></figure>

* Screenshot 8: Modified POST request with puserid=1 in request body demonstrating ability to update different user's profile by manipulating puserid parameter, with "Send" button highlighted showing active exploitation.

<figure><img src="../../../.gitbook/assets/image (330).png" alt=""><figcaption></figcaption></figure>

* Screenshot 9: Response shows HTTP 302 redirect confirming successful unauthorized profile modification for userid=1, demonstrating complete IDOR exploitation allowing both profile viewing and modification of arbitrary users.
