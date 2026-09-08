# What is a Race Condition?

### What is a Race Condition?

A race condition occurs when an application processes multiple requests concurrently without proper synchronization. This can cause multiple requests to interact with the same data at the same time, leading to unintended behavior.

The short time period during which this collision can happen is called the **race window**.

***

### Limit Overrun Race Conditions

Limit overrun race conditions allow an attacker to exceed limits enforced by business logic.

**Common examples:**

* Redeeming a single-use coupon or gift card multiple times
* Withdrawing more money than the account balance
* Bypassing rate limits or CAPTCHA restrictions
* Reusing OTPs or verification tokens

These vulnerabilities are usually **TOCTOU (Time Of Check To Time Of Use)** flaws.

***

### Race Window

The race window is the small gap between:

* Checking a condition (for example, “has this coupon been used?”)
* Updating the data (marking the coupon as used)

If multiple requests hit this window simultaneously, the logic can fail.

***

### Detecting Limit Overrun Race Conditions

1. Identify a security-critical endpoint with limits (coupon, OTP, balance, etc.).
2. Send multiple identical requests in quick succession.
3. Observe whether the limit is bypassed.

Parallel request sending greatly increases success.

***

### Hidden Multi-Step Sequences

A single HTTP request may trigger multiple internal steps on the server.\
These intermediate internal states are called **sub-states**.

Sub-states:

* Exist only for milliseconds
* Are invisible to the user
* Can expose powerful race condition vulnerabilities

***

### Race Condition in Authentication (MFA Example)

In some applications, a user session is created **before** MFA enforcement is completed.

This creates a temporary sub-state where:

* The user is logged in
* MFA is not yet enforced

An attacker can exploit this by sending a login request and an authenticated request in parallel, potentially bypassing MFA.

***

### Predict – Probe – Prove Methodology

Proposed by PortSwigger Research

#### 1. Predict

* Focus only on security-critical endpoints
* Look for endpoints that modify the same record (user, order, session, token)

#### 2. Probe

* Benchmark normal behavior using sequential requests
* Send the same requests in parallel
* Look for any deviation in responses or application behavior

#### 3. Prove

* Reduce unnecessary requests
* Reproduce the issue reliably
* Understand the underlying state machine weakness

***

### Multi-Endpoint Race Conditions

These occur when multiple endpoints are processed at the same time.

**Example:**

* Payment validation endpoint
* Cart modification endpoint

If both are triggered during the same race window, items may be added after payment validation but before order confirmation.

***

### Aligning Race Windows

Race attacks can fail due to:

* Backend connection delays
* Different processing times per endpoint

#### Connection Warming

Sending harmless requests first (like loading the homepage) can stabilize backend connections and reduce timing issues.

***

### Single-Endpoint Race Conditions

A race condition can occur even on a single endpoint if:

* Multiple requests are sent in parallel
* Each request uses different input values
* Shared session data is modified

**Example:**\
Password reset requests for two users sent in parallel may mix user IDs and tokens in the session.

Email-based workflows are especially vulnerable due to asynchronous processing.

***

### Defensive Best Practices

* Use database-level integrity controls (UNIQUE constraints, transactions)
* Do not rely on sessions to protect database limits
* Update session variables atomically, not individually
* Be cautious with ORMs; hidden transactions are still your responsibility
* Consider stateless designs (e.g., JWTs), but be aware of their own risks

***

### Key Takeaway

Race conditions are not just timing bugs — they are **state machine failures**.\
Any system with multi-step logic, shared state, or concurrent processing can be vulnerable if not designed carefully.
