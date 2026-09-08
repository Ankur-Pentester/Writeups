# What is API testing ?

## API Recon and Attack Surface Discovery

API recon is the foundational step in API security testing. Before attempting exploitation, it is essential to understand how an API is structured, how it behaves, and what functionality it exposes. The goal of API recon is to discover as much information as possible about the API in order to identify its full attack surface.

***

### 1. Using Machine-Readable API Documentation

Machine-readable API documentation is one of the most valuable sources of information during API recon. These documents are typically written in structured formats such as **JSON** or **YAML**, allowing automated tools to parse and analyze them.

Common examples include:

* `openapi.json`
* `swagger.json`
* `swagger.yaml`

These files often reveal:

* All available API endpoints
* Supported HTTP methods
* Required and optional parameters
* Authentication mechanisms
* Data schemas and response formats

#### 1.1 Analyzing Documentation with Automated Tools

You can use several tools to analyze machine-readable API documentation:

* **Burp Scanner**\
  Burp Scanner can crawl and audit OpenAPI documentation to automatically discover endpoints and potential vulnerabilities.
* **OpenAPI Parser (Burp BApp)**\
  This extension parses OpenAPI specifications and imports all documented endpoints directly into Burp’s site map.
* **Postman** and **SoapUI**\
  These tools allow you to manually test documented endpoints by sending requests and observing responses, which helps validate how the API behaves in practice.

> If API documentation is available, it should always be the first target during recon, as it often exposes functionality that is not visible in the frontend.

***

### 2. Identifying API Endpoints via Application Browsing

Even when documentation is available, it should not be fully trusted. Documentation may be outdated, incomplete, or inaccurate. Therefore, it is important to browse applications that actively use the API.

#### 2.1 Crawling the Application

* Use **Burp Scanner** to crawl the application.
* Browse the application manually using Burp’s built-in browser.
* Observe API traffic in Burp Proxy and Site Map.

This approach helps identify endpoints that are actually used by the application and may not be documented.

#### 2.2 Identifying Endpoint Patterns

While browsing, look for common API patterns in URLs, such as:

```
/api/
/api/v1/
/api/v2/
```

Endpoints containing `/api/` almost always indicate backend functionality and should be investigated thoroughly.

***

### 3. Extracting API Endpoints from JavaScript Files

JavaScript files often contain references to API endpoints that are not directly triggered through normal browser interaction.

You can extract these endpoints by:

* Allowing Burp Scanner to automatically extract links during crawling
* Using the **JS Link Finder** Burp extension for deeper endpoint extraction
* Manually reviewing JavaScript files in Burp

JavaScript analysis is especially useful for discovering:

* Internal APIs
* Deprecated endpoints
* Mobile-only or admin-only endpoints

***

### 4. Interacting with API Endpoints

Once endpoints have been identified, they should be tested manually to understand how they behave.

#### 4.1 Using Burp Repeater

Burp Repeater allows you to:

* Modify request parameters
* Change HTTP methods
* Alter request headers and bodies
* Observe detailed responses from the API

#### 4.2 Using Burp Intruder

Burp Intruder enables automated testing by:

* Fuzzing parameters
* Enumerating HTTP methods
* Discovering hidden endpoints and parameters

Careful analysis of responses and error messages is critical, as they often reveal valuable information about backend logic.

***

### 5. Identifying Supported HTTP Methods

Each API endpoint may support multiple HTTP methods, and each method can expose different functionality.

Common HTTP methods include:

* **GET** – Retrieve data
* **POST** – Create new resources
* **PATCH** – Apply partial updates
* **PUT** – Replace resources
* **DELETE** – Remove resources
* **OPTIONS** – Reveal supported methods

#### Example:

```
GET    /api/tasks
POST   /api/tasks
DELETE /api/tasks/1
```

Testing all possible HTTP methods may uncover unintended functionality, such as unauthorized deletion or modification of data.

> When testing destructive methods like DELETE, always target low-priority or test objects to avoid unintended impact.

***

### 6. Identifying Supported Content Types

API endpoints usually expect data in a specific format. Changing the content type can significantly affect how requests are processed.

Common content types include:

* `application/json`
* `application/xml`
* `application/x-www-form-urlencoded`

Changing the `Content-Type` header may:

* Trigger verbose error messages
* Bypass flawed input validation
* Expose differences in backend processing logic

For example, an API may be secure when handling JSON but vulnerable when processing XML (leading to issues such as XXE).

The **Content Type Converter** Burp extension can automatically convert request bodies between JSON and XML formats.

***

### 7. Discovering Hidden API Endpoints

Hidden endpoints often follow the same naming conventions as known endpoints.

#### Example:

If the following endpoint is identified:

```
PUT /api/user/update
```

You can use Burp Intruder to fuzz the endpoint path:

```
/api/user/§update§
```

Common payloads include:

```
create
delete
add
remove
admin
reset
```

This technique frequently reveals undocumented functionality that is not accessible through the frontend.

***

### 8. Discovering Hidden Parameters

APIs may accept undocumented parameters that significantly change application behavior.

Tools for discovering hidden parameters include:

* **Burp Intruder** – Using wordlists of common parameter names
* **Param Miner** – Automatically guesses thousands of parameter names based on application context
* **Content Discovery** – Finds unlinked parameters and content

***

### 9. Mass Assignment Vulnerabilities

Mass assignment (also known as auto-binding) occurs when frameworks automatically bind request parameters to internal object properties without proper validation.

#### 9.1 Identifying Mass Assignment

Consider the following PATCH request:

```json
{
  "username": "wiener",
  "email": "wiener@example.com"
}
```

And the corresponding GET response:

```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "isAdmin": false
}
```

This suggests that `id` and `isAdmin` are internal object fields that may also be modifiable.

***

#### 9.2 Testing for Mass Assignment

Add the hidden parameter to the PATCH request:

```json
{
  "username": "wiener",
  "email": "wiener@example.com",
  "isAdmin": false
}
```

Then test with an invalid value:

```json
{
  "isAdmin": "foo"
}
```

If the API responds differently, it indicates that the parameter is being processed.

Finally, attempt exploitation:

```json
{
  "isAdmin": true
}
```

If successful, the user may gain unauthorized administrative privileges.

***

### 10. Preventing API Vulnerabilities (Defensive Perspective)

To reduce API security risks, developers should:

* Restrict access to API documentation if it is not intended to be public
* Keep documentation accurate and up to date
* Enforce strict allowlists for HTTP methods
* Validate expected content types for all requests
* Use generic error messages to prevent information leakage
* Secure all API versions, including deprecated ones

#### Preventing Mass Assignment

* Allowlist fields that users are permitted to modify
* Blocklist sensitive fields such as `isAdmin`, `role`, and `userId`

### Final Summary

API recon is a critical phase of API security testing that involves analyzing documentation, browsing applications, extracting endpoints from JavaScript, and interacting with APIs using manual and automated tools. By testing HTTP methods, content types, hidden endpoints, and parameters, attackers may uncover serious vulnerabilities such as mass assignment. Securing APIs requires strict validation, proper documentation management, and careful control over exposed functionality.
