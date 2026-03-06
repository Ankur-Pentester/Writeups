# What is WebSocket Attack ??

## Manipulating WebSocket Traffic

WebSockets are widely used in modern web applications to enable **real-time communication** between a browser and a server. Unlike traditional HTTP requests, WebSockets maintain a **persistent connection**, allowing messages to be exchanged continuously in both directions.

Because WebSockets are often used to transmit **user actions and sensitive data**, they can also introduce security vulnerabilities if input validation and authentication mechanisms are not properly implemented.

Testing WebSocket security usually involves **manipulating WebSocket messages and connections** to observe how the application handles unexpected input.

Burp Suite is one of the most effective tools for testing WebSocket traffic.

Using Burp Suite, a tester can:

* Intercept and modify WebSocket messages
* Replay and generate new WebSocket messages
* Manipulate WebSocket connections and handshakes

***

## Intercepting and Modifying WebSocket Messages

Burp Suite allows testers to intercept WebSocket messages in real time and modify their contents before they reach the server.

### Steps

1. Open **Burp Suite**.
2. Launch the **Burp Browser**.
3. Navigate to a feature in the application that uses WebSockets (for example, a chat feature or live notifications).
4. In Burp Proxy, check the **WebSockets History** tab to confirm that WebSockets are being used.
5. Enable **Intercept** in the Proxy tab.
6. When a WebSocket message is sent, it will appear in the intercept window.
7. Modify the message if needed and click **Forward**.

***

### Example: Intercepting a WebSocket Message

Normal message sent from browser to server:

```
{"message":"Hello Carlos"}
```

A penetration tester might modify the message:

```
{"message":"Hello Hacker"}
```

This helps determine how the server processes modified input.

***

## Replaying and Generating WebSocket Messages

Sometimes testers need to **send the same message multiple times** or generate new messages to test different attack scenarios. This can be done using **Burp Repeater**.

### Steps

1. Go to **Proxy → WebSockets History**.
2. Select a WebSocket message.
3. Right-click and choose **Send to Repeater**.
4. In Repeater, edit the message as required.
5. Send the message repeatedly to observe the server's behavior.

***

### Example

Original WebSocket message:

```
{"action":"sendMessage","text":"Hello"}
```

Modified message:

```
{"action":"sendMessage","text":"Testing WebSocket"}
```

The tester can send this request multiple times to see how the server responds.

***

## Generating Custom WebSocket Messages

Burp Repeater also allows testers to create entirely new WebSocket messages that the application may not normally send.

Example:

```
{"action":"deleteUser","id":100}
```

If the server processes this message without proper authorization checks, it may result in a **privilege escalation vulnerability**.

***

## Manipulating WebSocket Connections

Before WebSocket communication begins, the browser and server perform a process known as the **WebSocket handshake**.

This handshake starts as an HTTP request.

### Example WebSocket Handshake Request

```
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

Server response:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
```

Once this handshake is completed, the connection switches from HTTP to WebSocket protocol.

***

### Why Manipulate the Handshake?

Manipulating the handshake can reveal additional attack surfaces. For example:

* Authentication tokens
* Session cookies
* Authorization headers

Example handshake with token:

```
GET /chat HTTP/1.1
Host: example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
Upgrade: websocket
Connection: Upgrade
```

If the token is modified or reused, it may allow **unauthorized access**.

***

## WebSocket Security Vulnerabilities

Many common web vulnerabilities can also occur in WebSocket-based applications.

These include:

* SQL Injection
* XML External Entity (XXE)
* Cross-Site Scripting (XSS)
* Server-Side Request Forgery (SSRF)
* Authentication and authorization flaws

***

## Example: Cross-Site Scripting (XSS) via WebSocket

Consider a chat application that uses WebSockets to send messages between users.

When a user sends a message, the following WebSocket message is transmitted:

```
{"message":"Hello Carlos"}
```

The server forwards the message to another user, where it is displayed in the browser:

```
<td>Hello Carlos</td>
```

If the application does not properly sanitize user input, an attacker could send the following message:

```
{"message":"<img src=1 onerror='alert(1)'>"}
```

This would be rendered as:

```
<td><img src=1 onerror='alert(1)'></td>
```

As a result, the JavaScript code executes in the victim's browser, leading to an **XSS vulnerability**.

***

## Example: SQL Injection via WebSocket

If WebSocket input is used directly in database queries without sanitization, SQL injection may occur.

Example malicious payload:

```
{"username":"admin' OR 1=1--"}
```

If the server constructs a query like:

```
SELECT * FROM users WHERE username='admin' OR 1=1--'
```

This could allow attackers to bypass authentication or extract database information.

***

## Example: SSRF via WebSocket

If the server processes URLs sent via WebSocket messages, attackers may exploit this to access internal services.

Example payload:

```
{"url":"http://127.0.0.1:8080/admin"}
```

This may allow attackers to access internal endpoints not normally accessible from the internet.

***

## Detecting WebSocket Vulnerabilities

To test WebSocket security effectively, penetration testers should:

1. Intercept WebSocket traffic using Burp Suite.
2. Modify message parameters and observe server responses.
3. Attempt injection attacks such as XSS or SQL injection.
4. Test for authentication and authorization bypass.
5. Analyze hidden or undocumented WebSocket actions.

***

## Conclusion

WebSockets provide powerful real-time communication for modern web applications, but they also introduce new attack surfaces.

Because WebSocket messages often contain **user-controlled data**, attackers may exploit improper input validation, weak authentication mechanisms, or insufficient access control.

By intercepting, modifying, and replaying WebSocket messages using tools like **Burp Suite**, security testers can identify and exploit vulnerabilities that might otherwise remain hidden.

Proper validation, authentication checks, and secure coding practices are essential to protect WebSocket-based applications from these threats.
