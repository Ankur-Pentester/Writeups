# API Hacking

### Vulnerability : Broken Object Level Authorization (IDOR) in Image Delete API

#### 1. Vulnerability Name & Description

**Challenge Name:** API Hacking – Wallpapers Dashboard

**Vulnerability Type:** Broken Object Level Authorization (BOLA) / Insecure Direct Object Reference (IDOR)

The application exposes an unauthenticated or improperly authorized API endpoint that allows users to delete arbitrary image files by directly specifying the image filename as a request parameter.

The backend does not verify **ownership**, **authorization**, or **allowed file scope**, allowing an attacker to delete any file present in the uploads directory.

#### 2. Proof of Concept (PoC)

**Vulnerable Endpoint:**

```
DELETE <http://localhost:1337/lab/api-hacking/api-hacking1/api/delete_image.php?image=><filename>
```

**Credentials Used:**

```
user/user
```

**Attack Payload Example:**

```
DELETE /api/delete_image.php?image=1_delete_me.jpg
```

#### Steps to Reproduce:

1. Login using valid low-privileged credentials (`user:user`)
2. Open the Wallpapers Dashboard
3. Intercept the **Delete** request using Burp Suite
4. Send the request to **Repeater**
5. Replace the `image` parameter value with any arbitrary filename
6. Send the modified request
7. The file is deleted successfully without authorization checks

***

#### 3. Impact Analysis

* Arbitrary deletion of files from the server
* Destruction of other users’ uploaded content
* Loss of application integrity
* Potential denial of service (DoS) by deleting critical assets
* Indicates missing access control at API level
* Can be chained with file upload or path traversal vulnerabilities

***

#### 4. CVSS 3.1 Score & Vector String

**CVSS Base Score:** **6.5 (Medium)**

**Vector String:**

```
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:H/A:L
```

***

#### 5. Risk Rating / Severity

**Severity Level:** **MEDIUM**

This vulnerability allows direct manipulation of server-side resources through API misuse. While it does not lead to immediate code execution, it significantly affects integrity and availability.

***

#### 6. Remediation / Mitigation Steps

1. Implement **object-level authorization checks**
   * Verify file ownership before deletion
2. Use **server-side file IDs** instead of raw filenames
3. Restrict API access using role-based access control (RBAC)
4. Validate file paths strictly (prevent traversal and arbitrary access)
5. Disable directory listing on production servers
6. Log and monitor destructive API actions

**Secure Example (PHP):**

```
if ($imageOwner !==$_SESSION['user_id']) { 
http_response_code(403); 
exit('Unauthorized');
}
```

#### 7. Screenshots / Logs / Payloads

<figure><img src="../../.gitbook/assets/image (263).png" alt=""><figcaption></figcaption></figure>

* Login with low-privileged user



<figure><img src="../../.gitbook/assets/image (264).png" alt=""><figcaption></figcaption></figure>

* Intercepted DELETE request in Burp Suite



<figure><img src="../../.gitbook/assets/image (265).png" alt=""><figcaption></figcaption></figure>

* Manual modification of image parameter



<figure><img src="../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

* Successful deletion response `("success": true)`

&#x20;

<figure><img src="../../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

* Apache directory listing confirming file removal

