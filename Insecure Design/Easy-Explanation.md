## 🔥 **What is Insecure Design?**

**OWASP defines Insecure Design as:**

> “Missing or ineffective control design, which allows attackers to exploit inherent risks.”

**In simple words:**

> *Your app works as expected… but the expected behavior itself is dangerous.*

This is **not a bug in code**, but a **flawed system architecture, logic, or workflow** that **creates security weaknesses.** The implementation may be perfect — the *idea* is flawed.

---

## 🧠 **Think of Insecure Design as...**

* Building a **house without a lock** on the main door. The door works fine. No bug. But design is insecure.
* Giving **admin access to all employees by default**.
* Allowing users to **change prices via the client-side**.

---

## 🎯 Key Differences

| Concept                  | Insecure Design                                | Insecure Implementation        |
| ------------------------ | ---------------------------------------------- | ------------------------------ |
| **Where the issue lies** | In the planning, architecture, or logic        | In the actual code             |
| **Example**              | Allowing users to cancel orders after delivery | Not validating tokens properly |
| **Fix requires**         | Changing the design or logic                   | Fixing the code                |

---

## ✅ **Examples of Insecure Design**

Let's break this down by real-world cases:

---

### ✅ Example 1: Password Reset with Token Reuse

**Design:**

* User receives a reset link via email.
* The reset token remains valid *even after* password change.

**Why it's insecure:**

* If someone gets access to that email (or intercepts the token), they can reuse it to reset the password again.

✅ **Fix Design:**

* Make the token **one-time-use**.
* **Expire** it immediately after use or after a short period.

---

### ✅ Example 2: Client-Side Price Control

**Design:**

* In an e-commerce app, the product price is passed from client to server via a hidden field.

```html
<input type="hidden" name="price" value="10.00" />
```

**Why it's insecure:**

* An attacker can **change the value** in the browser before submitting the form.

✅ **Fix Design:**

* Store prices **server-side** and reference them via **product ID** only.
* Never trust client-side data for sensitive fields like pricing.

---

### ✅ Example 3: No Rate Limiting on Login

**Design:**

* Login form allows **unlimited login attempts**.
* No rate limiting, lockout, or CAPTCHA.

**Why it's insecure:**

* Brute-force attack becomes trivial — attacker can guess passwords.

✅ **Fix Design:**

* Limit failed attempts.
* Lock out accounts temporarily.
* Use CAPTCHA after multiple failed logins.

---

### ✅ Example 4: Missing Role-Based Access Control (RBAC)

**Design:**

* Users can access URLs like `/admin/delete-user?id=7` just by typing them in the address bar.

**Why it's insecure:**

* No RBAC or permission check — attacker can act as admin.

✅ **Fix Design:**

* Implement strict **authorization checks**.
* Enforce **least privilege principle**.

---

### ✅ Example 5: Cancel Order After Shipment

**Design:**

* Application allows order cancellation without checking the order status.

**Why it's insecure:**

* A user can cancel **even shipped or delivered orders**, leading to financial loss.

✅ **Fix Design:**

* Only allow cancellations in **certain states** (e.g., pending, processing).

---

## 💣 Insecure Design is NOT...

| ❌ | It's NOT                                  |
| - | ----------------------------------------- |
| ❌ | A coding mistake                          |
| ❌ | A vulnerability scanner can’t detect this |
| ❌ | Something you can fix with a patch        |

It’s about **flawed assumptions, poor logic, or no security thinking during planning.**

---

## 🚨 Common Triggers of Insecure Design

1. 🚫 No security-focused design reviews.
2. 🧩 Business logic prioritizing UX over security.
3. 💼 Developers asked to "just ship it."
4. 📦 Reusing components without considering risk in your app context.
5. ⏰ Time pressure — cutting corners.

---

## 🔐 How to Prevent Insecure Design

### 1. **Threat Modeling Early**

* Do threat modeling at **design phase**: Identify what could go wrong, who could attack, how.

### 2. **Security Requirements**

* Clearly define what users can and can’t do.
* Include **security use cases** in design documents.

### 3. **Security Design Patterns**

* Use established patterns like:

  * RBAC (Role-Based Access Control)
  * Input validation layers
  * Session expiration strategies

### 4. **Defense in Depth**

* Multiple layers of security:

  * Server-side validation
  * Access control
  * Logging & alerts

### 5. **Security Review of Features**

* Before releasing a new feature, **ask security questions:**

  * Can this be abused?
  * Who has access to what?
  * Are edge cases handled?

---

## 🧠 Mindset Shift for Developers

> You must **design for failure** — think like an attacker.

Ask these before shipping:

* What happens if this input is tampered?
* What if someone replays this request?
* What if someone accesses it out of order?
* What if a user tries something unexpected?

---

## 📘 Final Thought: Why It Matters

**Insecure Design** is a **mindset issue**, not just a technical flaw. You can write the cleanest, bug-free code in the world — and still be vulnerable if your app logic is naive.

Fixing insecure design means:

* **Understanding your business logic**
* **Thinking like attackers**
* **Designing with security in mind from the start**

---


## ✅ INSECURE DESIGN — MASTERCLASS 🔐

---

## 🧠 **First: Clear the Concept**

**Insecure Design** is not a coding mistake. It’s when:

* The **feature itself is dangerous**, even if implemented correctly.
* Security controls are **missing**, or not considered at all.

Think of it like building a **car without brakes** — the engine runs fine (implementation), but design is flawed (no safety control).

---

## 📌 Let's Break it Down via Deep Real-World Examples

---

### 🔥 **Example 1: Password Reset Token Reuse**

---

### 📌 Scenario:

1. A user requests password reset.
2. Server sends a **link with a token** to their email.
3. The token is valid for 1 hour.
4. **After changing password**, token is still valid!

---

### ❌ Why it’s Insecure:

* Attacker who intercepts the token (e.g., via email access, Man-in-the-middle) can reuse it to reset the password again.

---

### ✅ Secure Design:

* Token must:

  * Be **single-use**
  * Expire **immediately after use**
  * Be **tied to a password version**, i.e., becomes invalid if password already changed.

---

### 🔍 Exploit Flow:

```plaintext
Attacker gains access to victim's email --> Extracts reset link -->
Victim resets password --> Attacker clicks reset link again -->
Attacker sets their own password --> FULL ACCOUNT TAKEOVER
```

---

---

### 🔥 **Example 2: Business Logic Flaw in Order Cancellation**

---

### 📌 Scenario:

You build an e-commerce app.

* Users can cancel orders via a button:

  ```
  POST /cancelOrder
  { "orderId": 88123 }
  ```

* The backend **only checks if the order exists**, and cancels it.

---

### ❌ Why it’s Insecure:

* There’s **no check on order status**.
* A user can cancel **after order has been shipped or delivered**.

---

### ✅ Secure Design:

* Orders should be cancelable **only in PENDING or PROCESSING** status.
* Add a check:

```java
if (order.getStatus() != OrderStatus.PENDING) {
    throw new IllegalStateException("Cannot cancel shipped/delivered orders");
}
```

---

### 🧠 Deeper Logic:

**Implementation is clean. Logic is flawed.**
A good attacker sees this and abuses it — refund fraud.

---

---

### 🔥 **Example 3: Price Tampering on Client Side**

---

### 📌 Scenario:

In your web app, you use a hidden input for price.

```html
<input type="hidden" name="price" value="1999.00">
```

**User can open DevTools → change price → Submit form.**

---

### ❌ Insecure Design:

* Relying on client-side values for critical logic like **pricing or payment** is extremely unsafe.

---

### ✅ Secure Design:

* On checkout, backend should:

  * Ignore the price sent by client
  * Fetch the **actual product price from DB**
  * Recalculate total on the server.

---

### 💥 Real-World Exploit:

Many **Indian e-commerce platforms** were hacked in early 2010s this way.
Attackers bought iPhones for ₹1.

---

---

### 🔥 **Example 4: No Rate Limiting on Login Endpoint**

---

### 📌 Scenario:

```http
POST /login
{ "email": "user@example.com", "password": "pass1234" }
```

Backend checks credentials and returns a token.

**But there's no limit on attempts.**

---

### ❌ What happens:

* Attacker uses brute-force script:

  ```bash
  for pass in password_list.txt; do
    curl -X POST /login -d '{"email":"user@example.com","password":"'$pass'"}'
  done
  ```

* They eventually guess the password.

---

### ✅ Secure Design:

* Implement rate limiting: Max 5 tries per minute per IP.
* Lock account after 5 failed tries (temporarily).
* Use CAPTCHA after 3 failed tries.

---

### 💥 Exploit Seen In:

* Credential stuffing attacks on banks and FinTech apps with weak login defenses.

---

---

### 🔥 **Example 5: Insecure URL-Based Access Control**

---

### 📌 Scenario:

* Admin panel endpoint: `/admin/delete-user?id=7`
* No role check. Just a link.

---

### ❌ Exploit:

* Normal users figure out they can just type URLs:

  ```
  /admin/delete-user?id=7
  ```

* No access control — they delete users.

---

### ✅ Secure Design:

* Use role-based access control:

```java
@PreAuthorize("hasRole('ADMIN')")
public ResponseEntity<?> deleteUser(Long id) { ... }
```

* Add checks both **in frontend and backend**.

---

---

### 🔥 **Example 6: Insecure Workflow - Account Lock**

---

### 📌 Scenario:

Banking app:

* User enters wrong password 5 times → account gets locked.
* **Unlock link sent via email**.

---

### ❌ Insecure Design:

* Attacker spams login attempts → locks real user accounts → floods user emails → denial-of-service.

---

### ✅ Better Design:

* Lock based on **IP + user ID** combination.
* Do not disclose "account locked" publicly.
* Use CAPTCHA to slow brute-force.

---

---

## 🧠 HOW TO PREVENT INSECURE DESIGN

---

### 🧰 1. **Threat Modeling**

* Ask "What can go wrong?" at **design phase**
* Tools: [OWASP Threat Dragon](https://owasp.org/www-project-threat-dragon/)

---

### 🧰 2. **Security Design Reviews**

* During design meetings, **check for abuse paths**:

  * Can user bypass this?
  * What if request is replayed?
  * What if status is tampered?

---

### 🧰 3. **Business Logic Testing**

* Use tools like **Burp Suite**, **OWASP ZAP** to manually test logic flaws.

---

### 🧰 4. **Security Controls in Architecture**

* Enforce:

  * Role-Based Access
  * Rate Limiting
  * Session Management
  * Secure Workflow State Transitions

---

### 🧰 5. **Secure Defaults**

* Never trust client input
* Whitelist access, don’t blacklist
* Fail safe: if error → deny access

---

## ✅ OWASP Suggestions for Secure Design:

* **Create Abuse Cases**: Plan for abuse, not just usage.
* **Establish Secure Design Patterns**
* **Use frameworks with built-in security** (Spring Security, ASP.NET Identity, etc.)

---

## 🔒 Summary Table

| Insecure Design Type      | Example                                     | Secure Fix                 |
| ------------------------- | ------------------------------------------- | -------------------------- |
| No access control         | Any user accesses `/admin/delete-user?id=5` | Enforce RBAC               |
| Weak session handling     | Token valid after logout                    | Invalidate token on logout |
| Client-controlled pricing | Price in form field                         | Server-side pricing        |
| Replayable reset token    | Token still works after reset               | One-time token             |
| Poor workflow logic       | Cancel after delivery                       | Check order status         |

---



# ✅ DEEP SECURE DESIGN CHECKLIST

### 🧠 For developers, architects, security testers & ethical hackers

---

## 🔹 **I. Authentication & Session Design**

| Check | Description                                                                                    |
| ----- | ---------------------------------------------------------------------------------------------- |
| ✅     | Use **strong authentication mechanisms** (e.g. 2FA, biometric, password policies).             |
| ✅     | Never expose **authentication logic** to the client (e.g. no client-side password validation). |
| ✅     | Enforce **rate limiting** on login attempts (e.g. 5 per minute/IP).                            |
| ✅     | Implement **account lockout** or CAPTCHA after repeated failures.                              |
| ✅     | Ensure **password reset tokens** are single-use, short-lived, and bound to user/session.       |
| ✅     | Invalidate **session tokens on logout, password change**, or after inactivity timeout.         |
| ✅     | Don’t rely solely on session cookies — enforce **CSRF protection**.                            |

---

## 🔹 **II. Access Control Design (Authorization)**

| Check | Description                                                                            |
| ----- | -------------------------------------------------------------------------------------- |
| ✅     | Design **Role-Based Access Control (RBAC)** from the beginning.                        |
| ✅     | Enforce access control checks **on every server-side endpoint**.                       |
| ✅     | Never rely on client-side enforcement (e.g. hiding buttons is not enough).             |
| ✅     | Validate that **users cannot access resources they don’t own** (e.g. `/user?id=2`).    |
| ✅     | Implement **object-level authorization checks** (especially for multi-tenant systems). |
| ✅     | Use **attribute-based access control (ABAC)** for complex permissions.                 |
| ✅     | Deny access by default, then explicitly allow — **"default deny" model**.              |

---

## 🔹 **III. Business Logic & Workflow Validation**

| Check | Description                                                                               |
| ----- | ----------------------------------------------------------------------------------------- |
| ✅     | Ensure all workflows are **state-aware** (e.g., don’t allow cancellation after shipping). |
| ✅     | Validate **workflow sequences**: one step must not proceed without the previous.          |
| ✅     | Don’t allow **important actions via GET** (e.g., GET `/delete-user`).                     |
| ✅     | Ensure users can’t repeat sensitive operations (e.g., cancel → refund → cancel again).    |
| ✅     | Protect business workflows from **logic abuse** (like fraud, price manipulation, etc.).   |
| ✅     | Ensure order, payment, refund flows have **validation gates**.                            |

---

## 🔹 **IV. Data Validation & Trust Boundaries**

| Check | Description                                                                           |
| ----- | ------------------------------------------------------------------------------------- |
| ✅     | Validate and sanitize **ALL inputs**, regardless of source (form, API, headers).      |
| ✅     | Do **strict server-side input validation** — length, type, format, range.             |
| ✅     | Never trust data from the client (even hidden fields, cookies, JWTs, etc).            |
| ✅     | Use **allow-lists (whitelists)** wherever possible instead of blacklists.             |
| ✅     | Validate **IDs or references** (e.g., `productId`, `userId`) against DB before using. |
| ✅     | Enforce proper **encoding/escaping** to prevent injection issues.                     |

---

## 🔹 **V. Secure Defaults & Error Handling**

| Check | Description                                                                         |
| ----- | ----------------------------------------------------------------------------------- |
| ✅     | Default behavior should be **secure even when misconfigured**.                      |
| ✅     | Disable or restrict all **debug, dev, or admin interfaces**.                        |
| ✅     | Don’t leak internal details in error messages (stack trace, SQL errors).            |
| ✅     | Log only **non-sensitive** error details for developers.                            |
| ✅     | Redirect errors (403, 500, etc.) to **friendly generic pages**.                     |
| ✅     | Avoid verbose or different error responses that help **enumerate system behavior**. |

---

## 🔹 **VI. Rate Limiting & Abuse Protection**

| Check | Description                                                                |
| ----- | -------------------------------------------------------------------------- |
| ✅     | Apply **rate limiting** on login, signup, file upload, payment, API calls. |
| ✅     | Use **captcha or puzzles** for repeated or automated requests.             |
| ✅     | Protect features from **automated abuse** (e.g., promo code spamming).     |
| ✅     | Throttle per **user, IP, session, and action type**.                       |
| ✅     | Log and alert for unusual spikes or abuse behavior.                        |

---

## 🔹 **VII. Secure Client-Side Design (Browser Apps, Mobile)**

| Check | Description                                                           |
| ----- | --------------------------------------------------------------------- |
| ✅     | Do not trust **anything from the frontend** (prices, roles, logic).   |
| ✅     | Never expose **API keys, secrets, or internal tokens** to the client. |
| ✅     | Ensure **sensitive data is not stored in localStorage or URL**.       |
| ✅     | Use secure cookies with `HttpOnly`, `Secure`, `SameSite` flags.       |
| ✅     | Implement **content security policies (CSP)** to prevent XSS.         |
| ✅     | Enforce **SSL/TLS everywhere** (frontend to backend & internal APIs). |

---

## 🔹 **VIII. API & Microservices Design**

| Check | Description                                                                  |
| ----- | ---------------------------------------------------------------------------- |
| ✅     | Validate auth and access control in **each microservice**, not just gateway. |
| ✅     | Don’t expose **internal APIs** or admin endpoints to the public internet.    |
| ✅     | Validate **schema and inputs** even for internal service calls.              |
| ✅     | Prevent **horizontal/vertical privilege escalation** across services.        |
| ✅     | Secure service communication via **mTLS or signed tokens**.                  |
| ✅     | Ensure **rate limiting & throttling** on all APIs (especially public ones).  |

---

## 🔹 **IX. Audit Logging & Monitoring**

| Check | Description                                                                     |
| ----- | ------------------------------------------------------------------------------- |
| ✅     | Log **security-relevant events**: login, password reset, privilege change, etc. |
| ✅     | Never log **passwords, tokens, or sensitive data** (credit card numbers, etc).  |
| ✅     | Detect abuse: brute-force, strange patterns, mass operations.                   |
| ✅     | Integrate with **SIEM or alerting systems** for visibility.                     |
| ✅     | Ensure logs are **tamper-resistant** and have integrity controls.               |

---

## 🔹 **X. Threat Modeling & Abuse Cases**

| Check | Description                                                                        |
| ----- | ---------------------------------------------------------------------------------- |
| ✅     | Do threat modeling before building the system: STRIDE, DREAD, PASTA.               |
| ✅     | Create **"abuse cases"** — how can this feature be used maliciously?               |
| ✅     | Involve **developers, testers, product managers, and security** in threat reviews. |
| ✅     | Identify all **trust boundaries**: frontend → backend, user → system, etc.         |
| ✅     | Continuously update models as system evolves (new features = new threats).         |

---

## 🧰 Suggested Tools (for Secure Design):

| Purpose                   | Tool                                                |
| ------------------------- | --------------------------------------------------- |
| 📘 Threat Modeling        | OWASP Threat Dragon, Microsoft Threat Modeling Tool |
| 🧪 Logic Flaw Testing     | Burp Suite Manual Testing                           |
| 📊 Rate Limiting Tests    | OWASP ZAP, K6                                       |
| ✅ Access Control Audit    | IAM review, Spring Security, Keycloak               |
| 🔍 Static/Dynamic Testing | Semgrep, Snyk, SonarQube                            |

---

## 📌 When to Use This Checklist:

| Phase                    | Usage                                      |
| ------------------------ | ------------------------------------------ |
| ✅ Requirements Gathering | Ask: “What could go wrong?”                |
| ✅ Architecture Design    | Review for threat modeling                 |
| ✅ Development Phase      | Implement security controls from the start |
| ✅ Pre-Deployment Review  | Final security verification                |
| ✅ Penetration Testing    | Guide for logic flaw tests                 |

---

# 🔐 Secure Design Checklist ✅ (With Checkboxes)

---

## 📁 PROJECT INFO

| Field                   | Value                       |
| ----------------------- | --------------------------- |
| Project Name            | `_________________________` |
| Reviewed By             | `_________________________` |
| Review Date             | `____/____/____`            |
| Threat Model Completed? | ☐ Yes ☐ No                  |

---

## ✅ Section 1: Authentication & Session Management

| # | Security Control                                                                | ✅ Done |
| - | ------------------------------------------------------------------------------- | ------ |
| 1 | ☐ Uses secure authentication (2FA, password policy, account lockout)            | ☐      |
| 2 | ☐ Password reset token is one-time-use, expires quickly, and invalid after use  | ☐      |
| 3 | ☐ Sessions expire after inactivity and logout invalidates token/session         | ☐      |
| 4 | ☐ Session tokens are signed, random, and stored securely (`HttpOnly`, `Secure`) | ☐      |
| 5 | ☐ CAPTCHA or challenge present after multiple failed logins                     | ☐      |
| 6 | ☐ No credentials stored in plaintext or front-end exposed config                | ☐      |

---

## ✅ Section 2: Access Control (Authorization)

| #  | Security Control                                                                | ✅ Done |
| -- | ------------------------------------------------------------------------------- | ------ |
| 7  | ☐ Role-Based Access Control (RBAC) is clearly defined and implemented           | ☐      |
| 8  | ☐ Access control enforced on **every endpoint** (not just UI-level)             | ☐      |
| 9  | ☐ Users can **only access their own data** (object-level security)              | ☐      |
| 10 | ☐ No privilege escalation possible via crafted requests or URLs                 | ☐      |
| 11 | ☐ Default access is denied (zero-trust) unless explicitly allowed               | ☐      |
| 12 | ☐ Access control also enforced across microservices (internal trust boundaries) | ☐      |

---

## ✅ Section 3: Business Logic & Workflow Integrity

| #  | Security Control                                                                    | ✅ Done |
| -- | ----------------------------------------------------------------------------------- | ------ |
| 13 | ☐ Critical operations are state-aware (e.g. cancel only pending orders)             | ☐      |
| 14 | ☐ Users can’t skip or reorder sensitive steps (multi-step flow enforced)            | ☐      |
| 15 | ☐ Repeated requests (replay) don’t create duplicate/abused actions                  | ☐      |
| 16 | ☐ Logic abuse cases like price tampering, refund manipulation tested                | ☐      |
| 17 | ☐ Backend never relies on client to determine important decisions (price, quantity) | ☐      |
| 18 | ☐ Admin-level actions cannot be accessed by regular users via URL manipulation      | ☐      |

---

## ✅ Section 4: Input Validation & Trust Boundaries

| #  | Security Control                                                          | ✅ Done |
| -- | ------------------------------------------------------------------------- | ------ |
| 19 | ☐ All user inputs are validated server-side (type, length, format, range) | ☐      |
| 20 | ☐ Inputs are sanitized and escaped to prevent XSS, SQLi, and injections   | ☐      |
| 21 | ☐ Backend does not trust any data from client (even hidden fields, JWTs)  | ☐      |
| 22 | ☐ APIs reject unexpected parameters and payloads                          | ☐      |
| 23 | ☐ All IDs and references are verified in DB for access and existence      | ☐      |

---

## ✅ Section 5: Rate Limiting & Anti-Automation

| #  | Security Control                                                             | ✅ Done |
| -- | ---------------------------------------------------------------------------- | ------ |
| 24 | ☐ Login, signup, and password reset are rate-limited per IP/user             | ☐      |
| 25 | ☐ Resource-intensive endpoints (file upload, search, checkout) are throttled | ☐      |
| 26 | ☐ CAPTCHA or challenge-response used after abuse detected                    | ☐      |
| 27 | ☐ Public APIs have rate limits and abuse detection                           | ☐      |
| 28 | ☐ Bulk operations (delete all, send all) require secondary confirmation      | ☐      |

---

## ✅ Section 6: Secure Defaults & Error Handling

| #  | Security Control                                                       | ✅ Done |
| -- | ---------------------------------------------------------------------- | ------ |
| 29 | ☐ Application denies access by default unless explicitly allowed       | ☐      |
| 30 | ☐ Error messages are user-friendly and leak no sensitive info          | ☐      |
| 31 | ☐ Stack traces and internal messages are not shown in production       | ☐      |
| 32 | ☐ Logging includes request ID and metadata but excludes sensitive data | ☐      |
| 33 | ☐ Developer and debug endpoints are disabled in production             | ☐      |

---

## ✅ Section 7: Secure Client-Side Behavior (Web/Mobile)

| #  | Security Control                                                 | ✅ Done |
| -- | ---------------------------------------------------------------- | ------ |
| 34 | ☐ All business logic and validation are duplicated on the server | ☐      |
| 35 | ☐ Sensitive data is never exposed in browser storage or URL      | ☐      |
| 36 | ☐ Content Security Policy (CSP) is enforced to mitigate XSS      | ☐      |
| 37 | ☐ Cookies use `HttpOnly`, `Secure`, and `SameSite=Strict` flags  | ☐      |
| 38 | ☐ No hardcoded secrets or tokens in JavaScript/mobile bundles    | ☐      |

---

## ✅ Section 8: API & Microservices Security

| #  | Security Control                                                            | ✅ Done |
| -- | --------------------------------------------------------------------------- | ------ |
| 39 | ☐ Authentication and authorization are enforced on each service             | ☐      |
| 40 | ☐ Services validate all incoming data and enforce schema (e.g. JSON schema) | ☐      |
| 41 | ☐ Internal services are not publicly accessible without API gateway         | ☐      |
| 42 | ☐ Service communication is encrypted (TLS/mTLS or signed tokens)            | ☐      |
| 43 | ☐ Services log unauthorized access attempts                                 | ☐      |

---

## ✅ Section 9: Logging, Auditing & Monitoring

| #  | Security Control                                                                  | ✅ Done |
| -- | --------------------------------------------------------------------------------- | ------ |
| 44 | ☐ Log all sensitive actions: login, role change, access violations, deletes       | ☐      |
| 45 | ☐ Logs do **not** contain passwords, tokens, card numbers, or PII                 | ☐      |
| 46 | ☐ Logs are tamper-proof and protected with write-only policies                    | ☐      |
| 47 | ☐ Alerting system exists for suspicious activities (e.g., multiple failed logins) | ☐      |
| 48 | ☐ Logs correlate actions with user IDs, IPs, timestamps                           | ☐      |

---

## ✅ Section 10: Threat Modeling & Secure Planning

| #  | Security Control                                                                       | ✅ Done |
| -- | -------------------------------------------------------------------------------------- | ------ |
| 49 | ☐ Threat modeling was conducted using STRIDE, DREAD, or PASTA                          | ☐      |
| 50 | ☐ Developers discussed **abuse cases** and attacker behaviors during design            | ☐      |
| 51 | ☐ Known attack patterns (e.g., replay attacks, IDOR, insecure redirects) are addressed | ☐      |
| 52 | ☐ Trust boundaries are clearly documented and protected                                | ☐      |
| 53 | ☐ Architecture decisions include security justifications (e.g., rate limit config)     | ☐      |

---

## 🧩 BONUS: Abuse Case Examples to Review During Design

| Abuse Scenario                                          | Reviewed? |
| ------------------------------------------------------- | --------- |
| ☐ Changing product price on client side                 |           |
| ☐ Canceling a delivered or refunded order               |           |
| ☐ Replaying password reset links                        |           |
| ☐ Accessing another user's data via ID manipulation     |           |
| ☐ Privilege escalation by modifying JWT or request body |           |
| ☐ Spamming with mass account creations                  |           |
| ☐ Brute-forcing login or promo codes                    |           |
| ☐ Bypassing business flow by reordering API calls       |           |

---

## 📘 Notes & Follow-Ups

```markdown
- Pending fixes or improvements:
  - ____________________________________________________
  - ____________________________________________________
  - ____________________________________________________

- Reviewer Suggestions:
  - ____________________________________________________
```

---

## 🧾 Completion Summary

| Completion Status                 | ✔️ |
| --------------------------------- | -- |
| Threat Model Completed            | ☐  |
| All Critical Controls Implemented | ☐  |
| Business Logic Secure             | ☐  |
| Signed Off by Security Team       | ☐  |

---




