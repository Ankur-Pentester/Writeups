# What is Web LLM Security !

### What is a Large Language Model (LLM)

A Large Language Model (LLM) is an AI system designed to process user input and generate human-like responses by predicting sequences of words. LLMs are trained on large public or semi-public datasets and are commonly accessed through chat-based interfaces using prompts.

**Common use cases include:**

* Customer support chatbots
* Language translation
* SEO and content generation
* Analysis of user-generated content

***

### LLM Prompt Structure

LLMs typically process instructions in three layers:

1. **System Prompt (Hidden, Highest Priority)**
   * Defines the model’s role and restrictions
   * Not visible to users
2. **Developer Prompt**
   * Defines application-specific behavior
3. **User Prompt (Lowest Priority)**
   * Input provided by the end user

***

### Web LLM Attacks (Overview)

Web LLM attacks exploit the fact that an LLM may have access to internal data or APIs that users cannot access directly. Attackers manipulate the model to misuse this access.

LLM attacks are conceptually similar to **Server-Side Request Forgery (SSRF)**, where a trusted server component is abused to reach protected resources.

***

### Prompt Injection

Prompt injection is a technique where an attacker crafts input designed to override or manipulate the LLM’s intended behavior.

**Types of prompt injection:**

* **Direct prompt injection** – Delivered directly through user input
* **Indirect prompt injection** – Delivered through external content consumed by the LLM

***

### Indirect Prompt Injection

Indirect prompt injection occurs when malicious instructions are embedded in external data that the LLM processes, such as:

* Web pages
* Emails
* API responses
* Training data

This can cause the LLM to perform unintended actions or generate malicious output affecting other users.

***

### Bypassing Indirect Prompt Injection Protections

Attackers may bypass safeguards by:

* Using fake system messages
* Injecting fake user responses
* Using misleading formatting or markers

This can confuse the model into treating data as trusted instructions.

***

### LLM APIs and Excessive Agency

LLMs are often integrated with backend APIs to perform actions such as managing users, orders, or emails.

**Excessive agency** occurs when an LLM has access to sensitive APIs and can be manipulated into using them unsafely, potentially leading to:

* Account takeover
* Data modification or deletion
* Privilege escalation

***

### Chaining Vulnerabilities in LLM APIs

Even APIs that appear harmless can be exploited when chained together.

Examples:

* File-handling APIs → Path traversal
* URL-fetching APIs → SSRF
* Database APIs → SQL injection

**Key principle:**\
All APIs accessible to an LLM should be tested as if they are publicly exposed.

***

### Insecure Output Handling

Insecure output handling occurs when an LLM’s responses are not properly validated or sanitized before being rendered or processed by other systems.

This can lead to:

* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Stored client-side attacks

***

### Training Data Poisoning

Training data poisoning occurs when an LLM is trained on compromised or untrusted data, causing it to produce incorrect, biased, or malicious responses.

**Common causes include:**

* Training on unverified data sources
* Overly broad datasets without proper filtering

***

### Training Data Leakage

Sensitive information may be unintentionally included in training data. Attackers can attempt to extract this data using prompt injection techniques such as:

* Sentence completion
* Context reminders
* Partial input reconstruction

This can result in unauthorized disclosure of sensitive user or system data.

***

### Defending Against LLM Attacks

#### Treat LLM-Accessible APIs as Public

* Enforce authentication and authorization
* Do not rely on the LLM to enforce access control

#### Avoid Feeding Sensitive Data to LLMs

* Sanitize training and fine-tuning datasets
* Apply least-privilege access to external data sources

#### Limit External Data Access

* Restrict API permissions
* Secure the entire data supply chain

#### Perform Regular Security Testing

* Test for prompt injection
* Monitor for data leakage

#### Do Not Rely on Prompt-Based Security

Prompt-based restrictions can often be bypassed using crafted or jailbreaking prompts and should not be considered a security control.

***

### Key Security Principles

* Any data consumed by an LLM may be disclosed
* Any API accessible to an LLM should be considered exposed
* Prompt-based controls are not a substitute for real security mechanisms
