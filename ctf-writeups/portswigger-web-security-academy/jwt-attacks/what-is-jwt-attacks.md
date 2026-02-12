# What is JWT Attacks ??

## JSON Web Token (JWT) Attacks

### Overview

JSON Web Tokens (JWTs) are widely used for:

* Authentication
* Session management
* Access control

A JWT vulnerability occurs when the server improperly verifies the token’s signature or mishandles its contents.

Since JWTs are self-contained, any flaw in verification can allow attackers to:

* Impersonate users
* Escalate privileges
* Completely bypass authentication

***

## JWT Structure

A JWT consists of three parts:

```
HEADER.PAYLOAD.SIGNATURE
```

Example:

```
eyJhbGciOiJSUzI1NiJ9
.
eyJ1c2VybmFtZSI6ImNhcmxvcyIsImlzQWRtaW4iOmZhbHNlfQ
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

#### 1️⃣ Header

Contains metadata such as the signing algorithm.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

#### 2️⃣ Payload

Contains user claims.

Example:

```json
{
  "username": "carlos",
  "isAdmin": false
}
```

#### 3️⃣ Signature

Generated using:

* Header
* Payload
* Secret or private key

If the signature is not verified properly, the token becomes vulnerable.

***

## Common JWT Attacks

***

### 1️⃣ Tampering with JWT Claims

If signature verification is weak or missing, attackers can modify payload values.

Original:

```json
{
  "username": "carlos",
  "isAdmin": false
}
```

Modified:

```json
{
  "username": "administrator",
  "isAdmin": true
}
```

Impact:

* Privilege escalation
* Account takeover

***

### 2️⃣ Unverified Signature (decode() Instead of verify())

Some developers mistakenly use token decoding instead of signature verification.

Incorrect:

```javascript
jwt.decode(token)
```

Correct:

```javascript
jwt.verify(token, secret)
```

If the signature is not verified, attackers can modify the token freely.

***

### 3️⃣ `alg: none` Attack

JWT allows the algorithm to be set to `"none"`.

Example:

```json
{
  "alg": "none"
}
```

If the server accepts unsigned tokens, attackers can remove the signature completely and bypass authentication.

Unsigned token format:

```
HEADER.PAYLOAD.
```

***

### 4️⃣ Weak Secret Key (HS256)

When using symmetric algorithms like HS256, the security depends on a secret key.

If the secret is weak (e.g., "secret", "admin", "password"), attackers can brute-force it.

After discovering the secret:

* Modify payload
* Re-sign token
* Gain full access

***

### 5️⃣ JWT Header Injection Attacks

Certain header parameters can introduce severe vulnerabilities.

***

#### 🔹 `jwk` Injection

If the server trusts the embedded `jwk` header:

```json
{
  "alg": "RS256",
  "jwk": { attacker_public_key }
}
```

Attackers can:

* Generate their own key pair
* Sign the token with their private key
* Embed their public key in the header

Impact: Full authentication bypass.

***

#### 🔹 `jku` Injection

`jku` allows the server to fetch keys from a URL.

Example:

```json
{
  "alg": "RS256",
  "jku": "https://attacker.com/jwks.json"
}
```

If the server does not enforce a strict whitelist, attackers can host their own key and sign arbitrary tokens.

***

#### 🔹 `kid` Injection

The `kid` parameter identifies which key to use.

Example:

```json
{
  "alg": "HS256",
  "kid": "key1"
}
```

If vulnerable to:

* Path traversal → `../../../../dev/null`
* SQL injection → `' OR 1=1--`

Attackers may force the server to use an arbitrary key.

***

### 6️⃣ Algorithm Confusion Attack

If the server expects RS256 but does not enforce algorithm restrictions, attackers may switch the algorithm to HS256 and misuse the public key as a symmetric secret.

Impact:

* Signature forgery
* Full authentication bypass

***

## Impact of JWT Vulnerabilities

JWT flaws can lead to:

* Authentication bypass
* Privilege escalation
* Account takeover
* Complete system compromise

Since JWTs control authentication, exploiting them often compromises the entire application.

***

## Prevention Best Practices

### Always verify the signature

Use strict verification methods and never rely only on decoding.

***

### Reject unexpected algorithms

Hardcode allowed algorithms on the server.

***

### Use strong secrets

Use long, random secrets for symmetric algorithms.

***

### Whitelist `jku` domains

Only allow trusted hosts for key retrieval.

***

### Validate `kid`

* Allow only safe characters
* Prevent path traversal
* Use parameterized queries

***

### Always include expiration (`exp`)

Short-lived tokens reduce impact if compromised.

***

### Avoid sending tokens in URLs

Use:

* Authorization headers
* HTTP-only cookies

***

### Use `aud` claim

Specify the intended audience to prevent cross-service token reuse.

***

### Implement token revocation

Support logout and blacklisting mechanisms.

***

## Conclusion

JWTs are powerful but dangerous if implemented incorrectly.

Any flaw in signature verification or key handling can allow attackers to forge tokens and gain unauthorized access.

Proper validation, strict configuration, and strong key management are essential to prevent JWT-based attacks.
