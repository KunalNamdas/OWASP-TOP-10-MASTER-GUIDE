## ✅ **Identification & Authentication Failures Checklist (Offensive Security Focused)**

---

### 🔐 1. Credential Stuffing & Brute-Force Attacks

* Are login endpoints vulnerable to password brute-force or credential stuffing?
* Is there no rate limiting or lockout mechanism after multiple failed login attempts?
* Can commonly leaked passwords (e.g., "123456", "Password\@123") be used successfully?
* Is there no CAPTCHA or bot protection on authentication forms?
* Are verbose error messages disclosing valid usernames/emails?

---

### 🧾 2. Password Policy & Weak Credentials

* Are weak passwords allowed (e.g., short length, no complexity)?
* Can accounts be created with common or breached passwords?
* Are there no restrictions on previously used passwords?
* Is the minimum password length < 8 characters?
* Is password complexity (uppercase, number, symbol) not enforced?

---

### 🪪 3. Username Enumeration

* Do login, registration, password reset, or MFA flows reveal valid usernames?
* Do error messages distinguish between “invalid username” vs “wrong password”?
* Can user IDs or emails be enumerated via API or search endpoints?
* Does GraphQL or REST return `200 OK` for non-existent users?

---

### 🧍 4. Multi-Factor Authentication (MFA) Weaknesses

* Is MFA optional or not enforced for sensitive actions?
* Is SMS-based MFA used (susceptible to SIM swap attacks)?
* Can MFA be bypassed via token reuse or weak session control?
* Are backup codes or TOTP secrets exposed or guessable?
* Is there no protection against brute-forcing MFA codes?

---

### 🔄 5. Session Management Flaws

* Are session IDs predictable, guessable, or reused?
* Is session ID not rotated after login or privilege escalation?
* Can sessions be reused across devices or IPs without re-authentication?
* Is session timeout too long or never enforced?
* Are expired or invalid tokens still accepted?

---

### 🍪 6. Insecure Authentication Tokens

* Are JWTs signed using `none` algorithm or weak secrets?
* Is JWT signature verification improperly implemented?
* Are tokens stored insecurely (e.g., in `localStorage` or URL)?
* Are access tokens long-lived and not rotated frequently?
* Can tokens be replayed across accounts (token reuse vulnerability)?

---

### 🧬 7. Improper OAuth / OpenID Connect Implementations

* Is `state` parameter missing or not validated (CSRF risk)?
* Can an attacker reuse tokens across clients (confused deputy)?
* Is token audience (`aud`) or issuer (`iss`) not validated?
* Are tokens issued to malicious or unregistered redirect URIs?
* Can `access_token` be obtained from an insecure endpoint or leaked in referrers?

---

### 📤 8. Insecure Forgot Password & Account Recovery

* Is the password reset token predictable, guessable, or not time-bound?
* Are reset tokens leaked via email, browser history, or logs?
* Can reset links be reused or shared across accounts?
* Is email/phone change allowed without verifying the new address?
* Is identity verification weak or bypassable (e.g., knowledge-based questions)?

---

### 🔁 9. Unauthenticated Functionality & Privileged Endpoints

* Are admin or user functions accessible without login (e.g., /admin/delete, /user/profile)?
* Are APIs callable without valid credentials?
* Is WebSocket authentication missing or flawed?
* Is re-authentication skipped for sensitive actions (e.g., deleting account, changing email)?

---

### 🧪 10. Insecure Authentication Workflows

* Can you bypass login by manipulating the client-side flow?
* Are multiple steps (e.g., email confirmation) skippable via direct access to the endpoint?
* Are confirmation emails predictable or reusable?
* Are tokens for onboarding, invites, or password reset not properly scoped?

---

### 🧱 11. SSO / SAML Misconfiguration

* Are SAML assertions not validated correctly (e.g., signature not checked)?
* Can an attacker forge SAML attributes to escalate privileges?
* Is SSO bypassed due to fallback login mechanisms?
* Are SSO tokens valid for multiple applications without scope?

---

### 🧾 12. Misconfigured Account Lockout or Throttling

* Is there no account lockout after multiple failed login attempts?
* Can account lockout be abused to deny access (DoS)?
* Is rate limiting only client-side (easy to bypass)?
* Are account lockout logs missing or unmonitored?

---

### 🧠 13. Improper Identity Claim Validation

* Can users change identity attributes (e.g., `user_id`, `email`) in JWT or JSON payloads?
* Are identity claims (e.g., in SSO, OAuth) not validated against session context?
* Are insecure user switching mechanisms present (e.g., admin impersonate mode unprotected)?

---

### 📂 14. Insecure API Authentication

* Do APIs lack proper token-based or session-based authentication?
* Can APIs be accessed with expired or revoked tokens?
* Are internal/private APIs exposed to unauthenticated users?
* Are API keys sent in headers/logs or stored insecurely in the frontend?

---

### 📁 15. Insecure Storage of Credentials

* Are credentials stored in plaintext in the database or logs?
* Are password hashes using weak algorithms (MD5, SHA1)?
* Are hardcoded credentials present in JavaScript, config files, or mobile apps?
* Are secrets committed in public GitHub repos or exposed via `.git` folders?

---



### 🔒 Offensive Security Checklist – Identification & Authentication Failures (IAF)

**✅ Points 16–30 (Advanced & Real-World Focused)**

---

**16.** ✅ **Enumerate Username via API error codes**
🧪 Test API login, forgot-password, or 2FA endpoints for different error messages on valid vs. invalid usernames.

**17.** ✅ **Check for hardcoded credentials in source maps or JS files**
🔍 Scan frontend source for `.map` files or JS leaks containing credentials or tokens.

**18.** ✅ **Test account lockout threshold bypass**
🔁 Use different IPs or usernames to rotate attempts and bypass account lockout mechanisms.

**19.** ✅ **Check for MFA token reuse or missing expiry**
🔁 Try to reuse OTP or TOTP after expiry to test for replay attacks.

**20.** ✅ **Brute-force via GraphQL or hidden API fields**
🧩 Explore GraphQL introspection or API schema abuse to perform IAF.

**21.** ✅ **Test SSO misconfiguration**
🧪 Try signing in via Google/Microsoft with a non-existent user and check access behavior.

**22.** ✅ **Check Auth0/Cognito/OpenID misconfigurations**
🔎 Investigate JWT audience validation, token leaks, and redirect misconfigs.

**23.** ✅ **Try "null" or blank passwords**
🔓 Attempt login using blank passwords or "null" values and observe backend response.

**24.** ✅ **Check for insecure password hints or reset links**
📩 Reset password flows that reveal sensitive hints can aid enumeration or guessing.

**25.** ✅ **Bypass login via caching headers**
🧨 Abuse misconfigured caching (`Cache-Control`, `ETag`, etc.) to replay authenticated sessions.

**26.** ✅ **Attempt JSON injection in login forms**
🛠 Try injecting JSON (e.g., `{"$ne": null}`) into fields to exploit NoSQL-style login bypasses.

**27.** ✅ **Check for insecure redirect after login**
🚨 Login flow redirects to `?next=` parameters — test for open redirects or phishing vectors.

**28.** ✅ **Check biometric spoofing (if supported)**
📱 Attempt face/image-based spoofing for mobile apps or biometric login flows.

**29.** ✅ **Assess insecure password complexity enforcement**
🧪 Test for short, common, or password list-based acceptance (e.g., "12345678").

**30.** ✅ **Check forgot-password OTP brute-force window**
📤 If OTPs aren't rate-limited, try brute-forcing 4–6 digit codes for account takeover.

---




### 🔐 **Identification & Authentication Failures – Offensive Checklist \[31–50]**

31. **Password Change w/o Old Password**
    Can user change password without verifying the old one?

32. **Token Reuse Allowed**
    Can a stolen session token be reused after logout?

33. **No IP Binding in Sessions**
    Try using the same session cookie from a different IP – still valid?

34. **Authentication Bypass via Null Byte Injection**
    Test login with payloads like `admin%00` or `admin\0`.

35. **Credential Stuffing**
    Try dumping common username/password pairs. No rate-limiting?

36. **Unverified Password Reset Link**
    Can you use a reset link without verifying email first?

37. **Session Hijack via Referer Header Leak**
    Is session token leaked via `Referer` to third-party sites?

38. **Weak Username Enumeration via Forgot Password**
    Do error messages differ for valid vs. invalid usernames?

39. **Broken MFA Logic**
    Try bypassing MFA by manipulating request flow (e.g., skipping MFA token step).

40. **Missing Device Recognition for MFA**
    Is MFA prompted every time, or is "trusted device" logic broken?

41. **Authentication via GET Requests**
    Can auth be performed via GET instead of POST? (`GET /login?u=admin&p=pass123`)

42. **Logout Doesn’t Invalidate JWT**
    After logout, can the JWT still be used to access resources?

43. **Token Rotation Not Implemented**
    Does the same refresh token work forever?

44. **Multiple Accounts with Same Email**
    Try registering multiple accounts with the same email — logic flaw?

45. **Token in URL Stored in Browser History**
    Does URL include auth token, leaking it to history or logs?

46. **Exposed Authentication Endpoints via Robots.txt**
    Check `robots.txt` for `/admin`, `/auth`, `/internal-login` etc.

47. **Session Tokens Not Revoked on Password Change**
    Are old sessions still valid after user changes password?

48. **Multiple Active Sessions per User Allowed**
    Does logging in elsewhere invalidate previous session? Should it?

49. **Authentication Over WebSockets Without Encryption**
    Is sensitive auth info transmitted over insecure WebSockets?

50. **Missing OAuth Scope Validation**
    Can you request more OAuth scopes than the app should allow?



