# Ticket (writeup)

**Based on the information provided in the challenge description, the Android package under investigation was identified as `com.strikebank.netbanking`. As instructed, I accessed the publicly available security review for this application via BeVigil using the URL format `https://bevigil.com/report/<package_name>`. This led me to the detailed report located at:**

```
<https://bevigil.com/report/com.strikebank.netbanking>
```

![image.png](<../../.gitbook/assets/image (1) (3).png>)

**Upon examining the BeVigil security report for the Android package `com.strikebank.netbanking`, several hardcoded secrets were identified within the application resources. These findings were listed under the ‘Strings’ section and included sensitive information such as API keys, encoded JWT secrets, and internal application credentials.”**

**“The associated files (e.g., `resources/res/values/strings.xml` and `sources/androidx/autofill/HintConstants.java`) contained hardcoded values, including an internal username (`tuhin1729`) and a plaintext password (`123456`) and `jwt_secret=c3RyIWszYjRua0AxMDA5JXN1cDNyIXMzY3IzNw==`. Exposure of such credentials significantly increases the attack surface, as attackers may leverage them to authenticate against backend portals or internal services connected to the application.**

Decoded `jwt_secret` :

```jsx
echo "c3RyIWszYjRua0AxMDA5JXN1cDNyIXMzY3IzNw==" | base64 -d
```

`deocoded_jwt_secret=str!k3b4nk@1009%sup3r!s3cr37`

![image.png](<../../.gitbook/assets/image (2) (3).png>)

Upon reviewing the extracted

```
strings.xml
```

file in the BeVigil report, I identified an entry containing the application’s backend portal URL. The tag

```
<string name="base_url">
```

revealed the following endpoint used by the mobile application:

```
<http://15.206.47.5.nip.io:8443/>
```

This URL appeared to be the primary entry point for the Strike Netbanking Portal.

![image.png](<../../.gitbook/assets/image (3) (3).png>)

Using the internal credentials (`tuhin1729` / `123456`) identified in the BeVigil strings analysis, I navigated to the backend portal (`http://15.206.47.5.nip.io:8443/`) and successfully authenticated into the Strike Bank Employee Dashboard. This confirmed that the leaked credentials were active in production and allowed unauthorized access to sensitive internal resources.

![image.png](<../../.gitbook/assets/image (4) (3).png>)

![image.png](<../../.gitbook/assets/image (5) (2).png>)

**After successfully authenticating to the employee portal using the hardcoded credentials, I examined the session storage and identified that the application uses a JWT-based authentication mechanism. The browser stored an `auth` cookie containing a JSON Web Token (JWT), which is used to maintain the logged-in session.”**

**“Earlier in the enumeration phase, the BeVigil report revealed the application’s JWT signing secret (`str!k3b4nk@1009%sup3r!s3cr37`). The presence of a hardcoded, publicly exposed JWT secret combined with a valid JWT token in the session enables full offline token forging. This effectively allows an attacker to modify or create arbitrary tokens, impersonate other users, escalate privileges, or completely bypass authentication.”**

![image.png](<../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png>)

**After extracting the authentication JWT from the browser session, I decoded the token to inspect the payload structure. The token was signed using the HS256 algorithm, meaning it relies on a shared secret for verification. Earlier during APK analysis, the BeVigil report disclosed the hardcoded JWT signing secret:`str!k3b4nk@1009%sup3r!s3cr37`.”**

**“Using the leaked secret, I generated a forged JWT via** [**jwt.io**](http://jwt.io) **by modifying the payload and setting the `username` claim to `admin`. The token was then re-signed with the exposed secret key, resulting in a fully valid administrative session token**

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNzY1MDE0NTcyfQ.j5OLTYRTzhOKhwkUac1Y7QvVWZ1ACQq_Dej3ToIut0Y`

![Screenshot From 2025-12-06 15-19-19.png](<../../.gitbook/assets/Screenshot From 2025-12-06 15-19-19.png>)

**After generating a forged JWT containing the payload `{ "username": "admin" }` and re-signing it using the leaked HS256 secret, I replaced the existing `auth` cookie with the newly crafted token. Upon refreshing the dashboard, the application validated the forged token and authenticated me as the administrative user.”**

**“The interface immediately reflected elevated privileges, displaying the message ‘You are logged in as admin.’ As part of the administrative view, the application disclosed the final challenge flag, confirming that full privilege escalation and authentication bypass had been achieved through JWT forgery.**

![Screenshot From 2025-12-06 15-19-26.png](<../../.gitbook/assets/Screenshot From 2025-12-06 15-19-26.png>)

```jsx
Flag : ClOuDsEk_ReSeArCH_tEaM_CTF_2025{ccf62117a030691b1ac7013fca4fb685}
```
