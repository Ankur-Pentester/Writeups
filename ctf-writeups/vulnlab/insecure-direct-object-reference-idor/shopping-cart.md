# Shopping Cart

### Vulnerability : Insecure Direct Object Reference (IDOR) – Shopping Cart Manipulation

#### 1. Vulnerability Name & Description

**Challenge Name:** Shopping Cart

**Insecure Direct Object Reference (IDOR) / Broken Access Control**

The shopping cart functionality relies on client-controlled parameters to manage cart items, prices, and purchase flow. The application does not properly validate object references or enforce authorization checks on the server side. By manipulating request parameters and directly accessing internal endpoints, an attacker can add unauthorized items to the cart, bypass balance restrictions, manipulate purchase flow, and complete transactions leading to negative balances and unauthorized item acquisition.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoints:**

* [http://localhost:1337/lab/idor/shopping-cart/add.php](http://localhost:1337/lab/idor/shopping-cart/add.php)

[http://localhost:1337/lab/idor/shopping-cart/cart.php](http://localhost:1337/lab/idor/shopping-cart/cart.php)

[http://localhost:1337/lab/idor/shopping-cart/3Dvalid.php](http://localhost:1337/lab/idor/shopping-cart/3Dvalid.php)

[http://localhost:1337/lab/idor/shopping-cart/messageBox.php](http://localhost:1337/lab/idor/shopping-cart/messageBox.php)

**Steps:**

1. Login as a normal user with an initial balance of `$1000`.
2. Add items to the cart using the **Add to Cart** button.
3. Observe that items are added successfully without server-side validation of user authorization.
4. Proceed to **Buy the Items** and note that purchase requests are processed based on client-controlled parameters.
5. Intercept and replay internal validation URLs (3D validation endpoints) directly.
6. Access the **Message Box** endpoint to retrieve OTP codes without proper access control.
7. Submit reused or manipulated OTP values to complete the purchase.
8. Successfully purchase restricted items and obtain the final flag.
9. Observe that the account balance becomes negative after the transaction.

***

#### 3. Impact Analysis

* Unauthorized purchase of restricted or premium items
* Bypass of balance and payment validation logic
* Negative account balance creation
* Exposure of sensitive verification data (OTP)
* Complete compromise of shopping cart integrity
* Business logic abuse leading to financial impact

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 8.1 (High)

**Vector String:**

CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:L/I:H/A:N

***

#### 5. Risk Rating / Severity

**Severity Level:** HIGH

This vulnerability allows attackers to bypass authorization and business logic controls, leading to unauthorized purchases, financial manipulation, and full compromise of the shopping workflow.

***

#### 6. Remediation / Mitigation Steps

1. Enforce strict server-side authorization checks for all cart and purchase actions
2. Validate ownership of cart items before processing requests
3. Prevent direct access to internal validation endpoints
4. Implement proper session-based access control for OTP and message endpoints
5. Recalculate prices and balances exclusively on the server side
6. Prevent negative balances and enforce transaction limits
7. Log and monitor abnormal purchase behavior

***

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>

* Initial shopping cart showing available items and prices and its have Product list with add-to-cart functionality and we add in cart successfully.

<figure><img src="../../../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (333).png" alt=""><figcaption></figcaption></figure>

* Our balance is $1000 and we also have a buy button then we click they say you don’t have a enough balance .

<figure><img src="../../../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>

* Let’s try the buy function and see what happened and its

<figure><img src="../../../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>

* buy successfully.

<figure><img src="../../../.gitbook/assets/image (336).png" alt=""><figcaption></figcaption></figure>

* We don’t have enough balance to buy it that’s by we use Direct access to internal validation endpoints

<figure><img src="../../../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>

* Direct access to internal validation endpoints

<figure><img src="../../../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

* Now this time we bypass the amount checking functionility and now we access the otp dashboard.

<figure><img src="../../../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>

* Now be copy the otp and paste on it otp page.

<figure><img src="../../../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>

* Now then we paste the otp and its valid and we finally buy the cart items

<figure><img src="../../../.gitbook/assets/image (341).png" alt=""><figcaption></figcaption></figure>

* Successful purchase confirmation
* Negative account balance after transaction
* Display of purchased items and final flag
