# What is HTTP Host header attacks ?

### Introduction

The HTTP `Host` header is a mandatory header in HTTP/1.1 requests. It specifies the domain name that the client wants to access. Modern web applications rely heavily on this header for routing traffic, virtual hosting, generating URLs, redirects, and communication between internal infrastructure components.

Because the `Host` header is fully user-controllable, improper validation can lead to severe vulnerabilities such as:

* Password reset poisoning
* Web cache poisoning
* Routing-based SSRF
* Authentication bypass
* Business logic flaws
* Internal virtual host access
* Connection state attacks

This document explains HTTP Host header attacks in depth, including how they work, why they happen, and how attackers exploit them in real-world environments.

***

## What is the HTTP Host Header?

A normal HTTP request looks like this:

```
GET /web-security HTTP/1.1
Host: portswigger.net
```

The `Host` header tells the server which domain the client wants to access.

Without the `Host` header, the server may not know which website or application should handle the request.

***

## Why is the Host Header Important?

### Old Internet Architecture

Historically:

```
1 IP Address = 1 Website
```

Example:

```
192.168.1.10 → example.com
```

No ambiguity existed.

***

### Modern Architecture

Today:

```
1 IP Address = Multiple Websites
```

Example:

```
192.168.1.10
 ├── example.com
 ├── api.example.com
 ├── shop.example.com
 └── admin.example.com
```

All websites may share the same IP address.

The server uses the `Host` header to determine which application should process the request.

***

## Virtual Hosting

This setup is called **Virtual Hosting**.

The server hosts multiple domains on a single IP address and routes requests based on the `Host` header.

Example:

```
GET / HTTP/1.1
Host: shop.example.com
```

The server loads the shop application.

***

## Reverse Proxies and Load Balancers

Modern infrastructure often contains intermediary systems:

```
User
 ↓
CDN
 ↓
Load Balancer
 ↓
Reverse Proxy
 ↓
Backend Servers
```

These components use the `Host` header for routing requests internally.

This creates additional attack surfaces.

***

## What is an HTTP Host Header Attack?

A Host header attack occurs when an application trusts user-controlled Host header input without proper validation.

Attackers manipulate the `Host` header to influence server-side behavior.

Example:

```
GET / HTTP/1.1
Host: evil.com
```

If the application trusts this value, it may:

* Generate malicious URLs
* Poison caches
* Misroute requests
* Access internal infrastructure

***

## Why Do Host Header Vulnerabilities Occur?

The root cause is usually:

```
Developers assume the Host header cannot be modified.
```

However, attackers can easily modify it using tools like:

* Burp Suite
* curl
* netcat
* custom scripts

Example:

```
GET / HTTP/1.1
Host: attacker.com
```

***

## Common Host Header Attack Scenarios

***

## Password Reset Poisoning

Applications sometimes generate absolute URLs using the `Host` header.

### Vulnerable Code

```
<?php$reset_link = "https://" . $_SERVER['HTTP_HOST'] . "/reset?token=" . $token;?>
```

***

### Normal Request

```
Host: example.com
```

Generated email:

```
https://example.com/reset?token=abc123
```

***

### Malicious Request

```
Host: attacker.com
```

Generated email:

```
https://attacker.com/reset?token=abc123
```

If the victim clicks the link, the token is sent to the attacker.

***

## Web Cache Poisoning

Caching systems may include the Host header in cache keys or generated responses.

Attackers can inject malicious content into shared caches.

Example:

```
GET / HTTP/1.1
Host: attacker.com
```

If cached, other users may receive poisoned responses.

***

## Routing-Based SSRF

Reverse proxies and load balancers often route traffic based on the `Host` header.

If improperly configured, attackers can force requests to internal systems.

***

### Example

```
GET / HTTP/1.1
Host: internal-admin.local
```

The proxy forwards the request internally.

Possible targets include:

* Internal admin panels
* Kubernetes dashboards
* Jenkins servers
* Cloud metadata APIs

***

## SQL Injection via Host Header

If the Host header is inserted into database queries:

```
SELECT * FROM sites WHERE host='$host'
```

It may become an SQL injection vector.

***

## Flawed Host Validation

Many applications validate the Host header incorrectly.

***

## Port Injection

Example:

```
Host: vulnerable-website.com:malicious
```

Some systems validate only:

```
vulnerable-website.com
```

while backend components process the entire value.

***

## Weak Subdomain Matching

Bad validation example:

```
if "vulnerable-website.com" in host:
```

Attacker bypass:

```
Host: notvulnerable-website.com
```

***

## Compromised Subdomain Abuse

If wildcard trust exists:

```
Host: hacked.vulnerable-website.com
```

Attackers may abuse less-secure subdomains.

***

## Ambiguous Requests

Different systems may parse requests differently.

This creates discrepancies between:

* Front-end servers
* Reverse proxies
* Back-end applications

***

## Duplicate Host Headers

Example:

```
GET / HTTP/1.1
Host: vulnerable-website.com
Host: evil.com
```

Front-end may use:

```
vulnerable-website.com
```

Back-end may use:

```
evil.com
```

This can lead to:

* SSRF
* Cache poisoning
* Authentication bypass

***

## Absolute URL Injection

Example:

```
GET https://vulnerable-website.com/ HTTP/1.1
Host: evil.com
```

Some systems trust the request line.

Others trust the Host header.

This discrepancy can be exploited.

***

## Line Wrapping Attacks

Example:

```
GET / HTTP/1.1 
 Host: evil.com
Host: vulnerable-website.com
```

Some servers interpret the indented header differently.

This can bypass validation mechanisms.

***

## Host Override Headers

Applications sometimes trust alternative headers.

Example:

```
X-Forwarded-Host: attacker.com
```

Common dangerous headers include:

```
X-Forwarded-Host
X-Host
X-Forwarded-Server
X-HTTP-Host-Override
Forwarded
```

***

## Example Attack

```
GET / HTTP/1.1
Host: vulnerable-website.com
X-Forwarded-Host: attacker.com
```

The frontend sees a valid host while the backend processes the malicious one.

***

## Connection State Attacks

HTTP/1.1 supports connection reuse:

```
One TCP connection → Multiple HTTP requests
```

Some vulnerable servers validate only the first request on a connection.

***

## Example

### First Request

```
GET / HTTP/1.1
Host: vulnerable-website.com
Connection: keep-alive
```

Validation succeeds.

***

### Second Request (same connection)

```
GET /admin HTTP/1.1
Host: evil.com
```

The server may skip validation because the connection is already trusted.

***

## SSRF via Malformed Request Line

Custom reverse proxies sometimes construct backend URLs unsafely.

***

## Vulnerable Logic

```
backend_url = "http://backend-server" + path
```

***

## Malicious Request

```
GET @private-intranet/admin HTTP/1.1
```

Generated backend URL:

```
http://backend-server@private-intranet/admin
```

Most HTTP libraries interpret this as:

```
username = backend-serverhost = private-intranet
```

Result:

```
Request sent to private-intranet
```

This creates SSRF.

***

## Routing-Based SSRF in Depth

Modern proxies and load balancers sit between:

```
Public Internet ↔ Internal Infrastructure
```

If they trust the Host header:

```
Host: internal-service.local
```

they may route requests into the internal network.

***

## Internal Network Targets

Common targets include:

* Redis
* Jenkins
* Grafana
* Elasticsearch
* Kubernetes Dashboard
* Docker API
* Cloud Metadata APIs

***

## AWS Metadata Example

```
Host: 169.254.169.254
```

This may expose cloud credentials.

***

## Detecting Routing-Based SSRF

Burp Collaborator can detect DNS lookups and outbound requests.

Example:

```
Host: attacker.burpcollaborator.net
```

If Collaborator receives traffic, the infrastructure may be vulnerable.

***

## Common Private IP Ranges

```
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Attackers often brute-force internal IP ranges.

***

## Testing Methodology

### 1. Modify Host Header

```
Host: attacker.com
```

***

### 2. Try Override Headers

```
X-Forwarded-Host: attacker.com
```

***

### 3. Test Duplicate Hosts

```
Host: example.comHost: attacker.com
```

***

### 4. Test Absolute URLs

```
GET https://example.com/ HTTP/1.1
Host: attacker.com
```

***

### 5. Observe Behavior

Look for:

* Redirect changes
* Password reset poisoning
* Reflection
* Cache poisoning
* Internal responses
* DNS lookups

***

## Prevention

***

## Avoid Absolute URLs

Prefer:

```
/login
```

instead of:

```
https://example.com/login
```

***

## Validate Host Header

Whitelist allowed domains.

Example (Django):

```
ALLOWED_HOSTS = [
    "example.com",
    "api.example.com"
]
```

***

## Reject Unknown Hosts

```
if host not in allowed_hosts:
    return 400
```

***

## Disable Host Override Headers

Disable unnecessary support for:

* X-Forwarded-Host
* X-Host
* Forwarded

***

## Separate Internal Services

Do not expose internal-only services through public reverse proxies.

***

## Use Strict Routing Rules

Load balancers should route only known domains.

***

## Conclusion

HTTP Host header attacks arise when applications or infrastructure components trust user-controlled host information.

Because modern architectures heavily depend on reverse proxies, CDNs, and load balancers, Host header vulnerabilities can lead to critical impacts such as:

* Account takeover
* SSRF
* Internal network access
* Cache poisoning
* Authentication bypass

Understanding how different systems parse and trust the Host header is essential for identifying and exploiting these vulnerabilities during security assessments.
