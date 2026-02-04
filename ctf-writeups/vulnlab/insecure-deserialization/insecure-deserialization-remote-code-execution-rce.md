# Insecure Deserialization – Remote Code Execution (RCE)

#### 1. Vulnerability Name & Description

**Challenge Name:** Random Nick Generator

**Vulnerability Type:** Insecure Deserialization (PHP Object Injection)

The application uses **Base64-encoded serialized PHP objects** stored in client-side cookies for session handling. The server blindly decodes and unserializes this data without validation.

An attacker can modify the serialized object, re-encode it, and send it back to the server, resulting in **arbitrary command execution**.

This leads to **remote code execution (RCE)** on the server.

***

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
<http://localhost:1337/lab/insecure-deserialization/random-nick-generator/index.php>
```

**Vulnerable Component:**

* Session cookie (Base64 encoded serialized PHP object)

***

#### 3. Exploitation Steps

***

#### Step 1: Login as Normal User

**Credentials Used:**

```
Username:test
Password:test
```

After successful login, the application redirects to the **Random Nick Generator** page.

***

#### Step 2: Extract Session Cookie

Using browser Developer Tools:

* Open **Storage → Cookies**
* Extract the session cookie value
* Observed value is **Base64 encoded**

***

#### Step 3: Decode Cookie Value

Using **CyberChef → From Base64**, the decoded value reveals a **serialized PHP object**:

```php
O:4:"User":6:{
  s:8:"username";s:4:"test";
  s:8:"password";N;
  s:16:"generatedStrings";a:0:{}
  s:7:"command";s:6:"system";
  s:8:"fileName";s:19:"randomGenerator.php";
  s:13:"fileExtension";s:3:"php";
}
```

This confirms the application unserializes user-controlled data.

***

#### Step 4: Modify Serialized Object (Command Injection)

The serialized object is modified to inject a system command.

Injected command payload:

```
id
```

Modified serialized object:

```php
s:7:"command";s:6:"system";
```

***

#### Step 5: Re-encode Payload

Using **CyberChef → To Base64**, the modified serialized object is encoded back to Base64.

***

#### Step 6: Replace Cookie & Refresh Page

* Replace the original cookie value with the malicious Base64 payload
* Refresh the page

***

#### Step 7: Remote Code Execution Achieved

After clicking **Generate Nick**, the injected command executes on the server.

**Command Output:**

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Confirms **successful remote code execution** as `www-data`.

***

#### 4. Impact Analysis

* Arbitrary command execution on server
* Full server compromise possible
* Access to sensitive files and credentials
* Potential web shell upload
* Privilege escalation opportunities
* Complete application takeover

***

#### 5. CVSS 3.1 Score & Vector String

**CVSS Base Score:** 9.8 (Critical)

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
```

***

#### 6. Risk Rating / Severity

**Severity Level:** **CRITICAL**

This vulnerability allows unauthenticated attackers to execute arbitrary OS commands, leading to full system compromise.

***

#### 7. Remediation / Mitigation Steps

1. **Never unserialize user-controlled data**
2. Use JSON instead of PHP serialization
3. Implement strict allow-listing of classes during unserialize
4. Disable PHP magic methods (`__wakeup`, `__destruct`)
5. Use signed and encrypted session tokens
6. Store session data server-side only
7. Apply least-privilege permissions to web server user

***

#### 8. Screenshots / Evidence

<figure><img src="../../../.gitbook/assets/image (236).png" alt=""><figcaption></figcaption></figure>

* Login page

<figure><img src="../../../.gitbook/assets/image (237).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (238).png" alt=""><figcaption></figcaption></figure>

* Extracted Base64 session cookie

<figure><img src="../../../.gitbook/assets/image (239).png" alt=""><figcaption></figcaption></figure>

* Decoded serialized PHP object

<figure><img src="../../../.gitbook/assets/image (240).png" alt=""><figcaption></figcaption></figure>

* Modified serialized payload
* Re-encoded Base64 cookie

<figure><img src="../../../.gitbook/assets/image (241).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (242).png" alt=""><figcaption></figcaption></figure>

* Command execution output (id)
* Proof of execution as www-data

