# Ticket Sales

## Vulnerability Report : Business Logic Vulnerability - Price Manipulation

**Challenge Name:** Ticket Sales

### 1. Vulnerability Name & Description

**Business Logic Vulnerability - Parameter Tampering / Price Manipulation**

Business Logic Vulnerabilities occur when an application's workflow or validation logic can be exploited to manipulate critical business operations. In this case, the ticket sales system contains a flaw where attackers can manipulate price-related parameters to purchase items at arbitrary prices, including negative values or zero. The vulnerability exists because the application accepts client-controlled pricing parameters without proper server-side validation and price integrity checks. Attackers can intercept purchase requests and modify hidden parameters like "ticket\_money" to set their own prices, effectively bypassing the intended payment system. This allows purchasing tickets for free or even receiving money instead of paying by using negative values.

### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:** [http://localhost:1337/lab/idor/ticket-sales/index.php](http://localhost:1337/lab/idor/ticket-sales/index.php)

**Vulnerable Parameters:** amount (number of tickets), ticket\_money (price per ticket)

**Normal Functionality:**

1. Access Ticket Sales page at [http://localhost:1337/lab/idor/ticket-sales/](http://localhost:1337/lab/idor/ticket-sales/)
2. Page displays: "The price of one ticket is 10 $"
3. Current account balance shows: "Amount of money in your account: 990 $"
4. System shows successful purchase: "Number of tickets you bought: 1"
5. Previous purchase cost: "Money you pay: 10 $"
6. User can enter number of tickets to purchase

**Business Logic Exploitation:**

**`Attack Steps:`**

1. Enter quantity in "Enter the number of tickets" field
2. Intercept purchase request using Burp Suite Proxy
3. Observe POST request to /lab/idor/ticket-sales/index.php
4. Request body contains parameters: `amount=100&ticket_money=0.1`
5. Identify ticket\_money parameter controls price per ticket
6. Notice client controls both quantity AND price parameters
7. Modify ticket\_money parameter to: `ticket_money=0.1`
8. Right-click request and select "Do intercept" then "Response to this request"
9. Forward manipulated request to server
10. Server accepts tampered price without validation

**Exploitation Result:**

1. Purchase completes successfully
2. Account balance increases to: "Amount of money in your account: 1000 $"
3. System shows: "Number of tickets you bought: 100"
4. System shows: "Money you pay: 0 $"
5. Successfully purchased 100 tickets for $0 (should cost $1000)
6. Gained financial advantage through price manipulation

### 3. Impact Analysis

* **Financial Loss:** Direct monetary loss through fraudulent free or discounted purchases
* **Revenue Theft:** Attackers can acquire products/services without payment
* **Account Balance Manipulation:** Using negative prices to increase account balance
* **Inventory Depletion:** Mass purchase of tickets at zero cost depletes available inventory
* **Business Process Bypass:** Complete circumvention of payment processing system
* **Competitive Advantage:** Resellers can acquire tickets at no cost and resell at market price
* **Market Manipulation:** Attackers can corner ticket market through free mass purchases
* **Audit Trail Issues:** Transaction logs show purchases but with incorrect pricing
* **Confidentiality: LOW** - No direct data disclosure
* **Integrity: CRITICAL** - Complete compromise of pricing and financial transaction integrity
* **Availability: MEDIUM** - Inventory depletion affects legitimate customers
* **Financial Impact:** Unlimited financial loss potential, revenue destruction
* **Business Impact:** Business model failure, inability to generate revenue, bankruptcy risk
* **Legal Liability:** Fraud charges against exploiters, but business reputation damage
* **Stakeholder Impact:** Loss of investor confidence, shareholder value destruction

### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 7.5 (High)

**Vector String:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:H/A:N`

### 5. Risk Rating / Severity

**SEVERITY: CRITICAL**

**Risk Level:** IMMEDIATE ACTION REQUIRED - EMERGENCY

Business logic vulnerabilities affecting financial transactions represent existential threats to business operations. Price manipulation allowing free or negative-cost purchases requires immediate emergency remediation.

### 6. Remediation / Mitigation Steps

1. **Server-Side Price Validation:**
   * Never accept price values from client-side requests
   * Always retrieve pricing from server-side database or configuration
   * Validate all financial calculations server-side before processing
   * Reject any requests containing price-related parameters from client
2. **Implement Price Integrity Checks:**
   * Store product prices in secure server-side database
   * Calculate total price server-side: price = quantity × unit\_price
   * Verify calculated price matches expected values
   * Use cryptographic signatures for price verification if prices must be client-side
3. **Remove Client-Controlled Pricing:**
   * Only accept quantity from client input
   * Retrieve current price from secure backend system
   * Calculate final price server-side only
   * Never trust client-provided price parameters
4. **Input Validation:**
   * Validate quantity is positive integer
   * Enforce maximum purchase limits per transaction
   * Check available inventory before processing
   * Verify sufficient account balance
5. **Transaction Monitoring:**
   * Implement anomaly detection for unusual purchase patterns
   * Alert on zero-value or negative-value transactions
   * Monitor for bulk purchases at unusual prices
   * Flag accounts with suspicious transaction history

### 7. Screenshots / Logs / Payloads

<figure><img src="../../../.gitbook/assets/image (303).png" alt=""><figcaption></figcaption></figure>

Screenshot 1: Ticket Sales page displays ticket price "10 $" and account balance "990 $" with purchase history showing "Number of tickets you bought: 1" and "Money you pay: 10 $" demonstrating normal transaction.

<figure><img src="../../../.gitbook/assets/image (304).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (305).png" alt=""><figcaption></figcaption></figure>

Screenshot 2: Burp Suite intercepts POST request showing vulnerable parameters amount=100\&ticket\_money=0.1 revealing client controls both quantity and price, with context menu showing "Do intercept" and "Response to this request" options.

<figure><img src="../../../.gitbook/assets/image (306).png" alt=""><figcaption></figcaption></figure>

Screenshot 3: After manipulating ticket\_money parameter, page displays account balance increased to "1000 $" with "Number of tickets you bought: 100" and "Money you pay: 0 $" confirming successful price manipulation allowing 100 tickets purchased for free.
