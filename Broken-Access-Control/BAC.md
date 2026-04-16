## 🛡️: Broken Access Control

---

### 🔹 Types

* IDOR (Insecure Direct Object References)
* Privilege Escalation
* Forced Browsing
* Method Tampering
* Client-side Enforcement
* CORS Misconfiguration
* Banking App - IDOR

---

### 🔹 Real-World Scenarios

* Profile Update → Role Escalation
* Delete User → No Validation
* View 2FA of Others

---

### 🔹 Testing Techniques

* Parameter Tampering
* JWT Manipulation
* Path Traversal
* Method Fuzzing
* Session Replay
* Hidden Admin Routes
* URL ID Change
* API Payload Tamper
* Role Field Injection

---

### 🔹 Exploitation Vectors

* Force Browsing
* Unprotected Endpoints
* Weak Token Checks
* Server-side Validation

---

### 🔹 Prevention

* RBAC (Role-Based Access Control) Implementation
* Random Object IDs
* Audit Logs
* Deny by Default

---

### 🔹 Tools

* Burp Suite
* Postman
* jwt\_tool
* ffuf
* ZAP
* Autorize
* Tamper Object IDs?

---

### 🔹 Checklist ✅

* Access Admin as User?
* Modify Roles?
* Replay Sessions?
* Hidden APIs?

---


# 🔐 **Chapter 1: Broken Access Control – Deep Dive**

---

## 🚨 What is Broken Access Control?

**Broken Access Control (BAC)** occurs when an application **fails to properly enforce user permissions**, allowing attackers to perform actions or access data outside their authorized scope.

> 🔎 **Example**: A normal user changing their role to “admin” via a crafted request or accessing `/admin/dashboard` directly.

---

## 🧠 Why Is It Critical?

* **OWASP Top 10 #1 (2021)**: It’s the **most commonly exploited vulnerability**, leading to **data breaches**, **privilege escalation**, and even **full account takeover**.
* BAC flaws are often **missed by automated scanners** and require **manual logic testing**.

---

## 🔄 Real-World Examples

| Case                           | Description                                                                       |
| ------------------------------ | --------------------------------------------------------------------------------- |
| ✅ IDOR                         | Changing `user_id=123` to `user_id=124` exposes another user’s profile.           |
| ✅ Privilege Escalation         | Changing your own role from “user” to “admin” via hidden fields or API tampering. |
| ✅ Unrestricted Admin Endpoints | Normal user accessing `/admin/settings` with no checks in place.                  |
| ✅ Method Tampering             | Changing HTTP method from `GET` to `PUT` to edit data.                            |
| ✅ CORS Misconfig               | Misconfigured CORS headers allow cross-origin access to protected data.           |

---

## 🔍 Attack Surface Overview

| Category                   | Targets                              |
| -------------------------- | ------------------------------------ |
| **Endpoints**              | `/admin`, `/settings`, `/users/{id}` |
| **Parameters**             | `role=`, `user_id=`, `isAdmin=true`  |
| **Methods**                | `GET`, `POST`, `PUT`, `DELETE`       |
| **Tokens**                 | JWTs, session cookies                |
| **Client-Side Validation** | Disabled buttons, hidden form fields |

---

## 🧪 How Do You Test for It?

### 1. 🔁 **Access Control Bypass Attempts**

| Action                              | What to Do                                   |
| ----------------------------------- | -------------------------------------------- |
| Try accessing other user IDs        | `/user/124`, `/invoice/another_id`           |
| Try accessing admin-only routes     | `/admin`, `/settings`, `/api/admin/*`        |
| Change HTTP methods                 | `POST` → `DELETE`, `GET` → `PUT`             |
| Replay session from lower privilege | Capture admin request, replay as normal user |
| Remove or manipulate tokens         | Remove `Auth` header, change JWT claims      |

---

### 2. 🛠️ **Test Cases to Try (Manual)**

* Modify role during registration (e.g., `role=admin`)
* Check for forced browsing (`/admin/dashboard`)
* Test parameter-based access (e.g., `user_id=999`)
* Tamper with JWT: set `role=admin`
* Modify hidden inputs in HTML forms
* Replay requests of higher privilege users

---

## 🔧 Tools to Use

| Tool                       | Use                                                   |
| -------------------------- | ----------------------------------------------------- |
| **Burp Suite**             | Intercept, modify, and replay requests                |
| **ZAP**                    | Interception & active/passive scans                   |
| **Postman**                | Manual testing of API endpoints                       |
| **JWT\_Tool / jwt.io**     | Tampering & decoding JWTs                             |
| **Autorize (Burp Plugin)** | Detect BAC by comparing responses                     |
| **ffuf**                   | Fuzz for hidden files/dirs (e.g., `/admin`, `/staff`) |

---

## ✅ Prevention Techniques (For Developers)

| Strategy                    | Description                                                          |
| --------------------------- | -------------------------------------------------------------------- |
| ❌ Never Trust Client        | All permissions must be checked server-side.                         |
| ✅ Enforce RBAC              | Apply strict **Role-Based Access Control** everywhere.               |
| 🆔 Use Indirect Identifiers | Avoid numeric IDs; use UUIDs instead.                                |
| 🔐 Deny by Default          | Default response for unknown/unauthorized should be `403 Forbidden`. |
| 🧾 Logging & Auditing       | Keep track of sensitive access patterns.                             |

---

## 📋 Basic Checklist ✅

| Check                                                 | Status |
| ----------------------------------------------------- | ------ |
| Can normal user access admin page?                    | ❌      |
| Can user change roles or permissions?                 | ❌      |
| Are internal API routes protected?                    | ✅      |
| Can another user’s data be accessed by changing `id`? | ❌      |
| Are HTTP methods validated per user role?             | ✅      |

---

### 💡 Pro Tips

* Always test **unauthenticated, authenticated low-privilege, and admin** users.
* Look for **hidden APIs** in JavaScript files.
* Don’t ignore mobile apps — they often contain juicy BAC flaws.
* Focus on **state-changing** requests (`POST`, `PUT`, `DELETE`).

---

## 🧠 Real Pentester Mindset

✅ Think like a **curious attacker**:

* "What if I change the ID?"
* "What if I bypass this client-side button?"
* "What if I remove this cookie?"
* "What if I access `/admin` directly?"

---



## 🧩 Chapter 2: Types of Broken Access Control — In-Depth

---

### 1️⃣ **IDOR (Insecure Direct Object References)**

**Description:**
Occurs when an attacker can access or manipulate resources by changing references (like IDs) in the request URL or body without proper authorization.

**Examples:**

* `GET /user/1001` → change to `/user/1002`
* `GET /invoice/123.pdf` → access someone else's invoice

**Advanced Testing Tips:**

* Tamper with `user_id`, `order_id`, etc.
* Try hidden or sequential IDs.
* Use **Burp Suite's Intruder** or **ffuf** to automate ID fuzzing.

**Bug Bounty Hint:**
Look for numeric or UUID references in mobile/web apps. Try replacing your own ID with others’.

---

### 2️⃣ **Privilege Escalation**

**Description:**
When a low-privileged user can gain higher-level access (e.g., user becoming admin).

**Examples:**

* Changing role in request:

  ```json
  {"role":"admin"}
  ```
* Accessing `/admin/dashboard` as a normal user.

**Advanced Testing Tips:**

* Intercept registration/login requests.
* Replay authenticated requests as a low-privilege user.
* Look for `isAdmin: false` → change to `true`.

**Tools:**

* Burp Suite (Repeater)
* **Autorize plugin** (to test privilege misuse)

---

### 3️⃣ **Forced Browsing**

**Description:**
Manually navigating to restricted URLs not visible or linked in the UI but not properly protected.

**Examples:**

* `/admin/`
* `/backup/db.zip`
* `/internal/settings`

**Advanced Technique:**

* Use **ffuf**, **dirsearch**, or **gobuster** to discover hidden directories.
* Look at JavaScript files for hidden paths.

**Bug Bounty Tip:**
Always check JS source for admin panels or backups exposed.

---

### 4️⃣ **Method Tampering**

**Description:**
Altering HTTP methods (`GET`, `POST`, `PUT`, `DELETE`) to bypass controls or trigger unintended behavior.

**Examples:**

* Changing `GET` to `PUT` → update resources.
* Changing `POST` to `DELETE` → delete others' data.

**Advanced Tools:**

* Burp Suite
* curl:

  ```bash
  curl -X PUT https://target.com/user/1
  ```

**Extra Tip:**
Combine with `X-HTTP-Method-Override` headers to bypass WAFs.

---

### 5️⃣ **Client-Side Enforcement**

**Description:**
Access control is validated on the client side (JS), which is easily bypassed.

**Examples:**

* Hidden buttons in the UI that perform actions.
* JavaScript disables admin options — remove JS checks.

**Testing Tip:**

* Inspect element / disable JS
* Modify localStorage/sessionStorage values

**Bug Bounty Bonus:**
Always test beyond what the UI shows. Don’t trust “disabled” buttons.

---

### 6️⃣ **CORS Misconfiguration**

**Description:**
Cross-Origin Resource Sharing (CORS) policy is too permissive, allowing attackers to make cross-origin requests and access sensitive data.

**Examples:**

* `Access-Control-Allow-Origin: *`
* Reflecting Origin header:
  Request with `Origin: evil.com` → Response allows it

**Advanced Check:**

* Use **CORS Exploit POC** tools or write custom JS
* Look for `Access-Control-Allow-Credentials: true`

**Bug Bounty Warning:**
CORS misconfigs can lead to session hijacking/data theft if credentials are allowed across origins.

---

### 7️⃣ **Banking App - IDOR**

**Special Case of IDOR**:
Banking, financial, or invoice-related apps where transaction IDs, bank account numbers, etc. are exposed.

**Testing Ideas:**

* View/edit another user's transactions
* Try unauthorized withdrawals/updates

**Bonus Bug Tip:**
Banking and finance apps have a **high impact scope**. Report with strong PoC and impact.

---

## ✅ Summary Table

| Type                    | Attack Vector                 | Tooling Tips    | Bug Bounty Focus                   |
| ----------------------- | ----------------------------- | --------------- | ---------------------------------- |
| IDOR                    | Modify IDs                    | Burp, ffuf      | Look for sequential/numeric IDs    |
| Privilege Escalation    | Tamper roles/fields           | Autorize, Burp  | Try admin-only APIs as normal user |
| Forced Browsing         | Discover hidden paths         | ffuf, gobuster  | Find unlinked endpoints, backups   |
| Method Tampering        | Change HTTP method            | curl, Burp      | Try `PUT/DELETE` instead of `GET`  |
| Client-side Enforcement | Disable JS or change frontend | DevTools        | Check for hidden buttons/fields    |
| CORS Misconfig          | Cross-origin requests         | curl, custom JS | Look for credentialed CORS flaws   |
| Banking IDOR            | Transaction data manipulation | Burp Intruder   | Focus on financial data, invoices  |

---


## ✅ **Chapter 3: Real-World Scenarios (Broken Access Control)**

---

### 🧩 **1. Profile Update → Role Escalation**

#### 📌 **Scenario:**

A regular user attempts to update their profile. The frontend sends a PUT or POST request like:

```json
{
  "username": "john_doe",
  "role": "admin"
}
```

#### 🧠 **Attack Logic:**

* Role-based fields should never be user-modifiable.
* If the backend fails to validate whether the user can assign the `admin` role, the user may become an admin.

#### 🔍 **Testing Steps:**

1. Intercept profile update request (via Burp Suite/Postman).
2. Add/modify the `role` or `isAdmin` field in the request body.
3. Submit the request and observe response or new privileges.

#### 🛡️ **Mitigation:**

* Server-side role validation.
* Never trust client-side data.
* Block privilege fields from being modified by low-privileged users.

---

### 🗑️ **2. Delete User → No Validation**

#### 📌 **Scenario:**

API endpoint:

```
DELETE /api/users/1002
```

Authenticated user with `user_id: 1001` tries to delete another user.

#### 🧠 **Attack Logic:**

* If backend only checks if a user is authenticated but not **authorized**, anyone can delete anyone else.

#### 🔍 **Testing Steps:**

1. Login as a user.
2. Modify the `user_id` in the DELETE request.
3. Observe whether deletion occurs (even if it's another user).

#### 🛡️ **Mitigation:**

* Verify that the authenticated user has permission to delete the specific resource.
* Use token/session-based identity matching.

---

### 👁️‍🗨️ **3. View 2FA of Other Users**

#### 📌 **Scenario:**

Endpoint:

```
GET /api/user/1002/2fa-settings
```

A user manually changes the user ID in the URL and views the 2FA configuration of others.

#### 🧠 **Attack Logic:**

* Sensitive settings like 2FA, email, etc., are exposed without verifying identity or access level.

#### 🔍 **Testing Steps:**

1. Locate sensitive endpoints in the account area.
2. Modify query/path parameters.
3. Validate if another user’s data is visible.

#### 🛡️ **Mitigation:**

* Enforce strict **access control checks** based on `user_id`.
* Mask or hide such sensitive configuration unless the requester owns it.

---

## 🔐 General Recommendations:

| Mistake                                        | Consequence                  | Fix                            |
| ---------------------------------------------- | ---------------------------- | ------------------------------ |
| Trusting `user_id` in requests                 | IDOR / Privilege Escalation  | Always check ownership         |
| Exposing all user settings via single endpoint | Data leakage                 | Use scoped tokens and filters  |
| Not validating actions (delete/update)         | Account takeover or deletion | Enforce RBAC checks on actions |

---

## 🧪 Pro Tips:

* 🔍 Use **Burp Suite’s Repeater + Autorize extension** to simulate access control bypasses.
* 🔧 Enable logging to detect unusual access patterns (like many user IDs being queried in sequence).
* 🧾 Include real user case scenarios in your bug bounty reports to increase credibility.

---

## 🔍 Chapter 4: Testing Techniques – Broken Access Control

Broken Access Control issues are often discovered through smart, methodical testing of how systems enforce user roles and object access. Below are the most **powerful and practical techniques** to find these flaws:

---

### 1. 🔧 **Parameter Tampering**

* **What It Is**: Manually changing request parameters (IDs, roles, emails, etc.) to test if you can access or manipulate other users' data.
* **Example**:

  ```
  GET /user/profile?user_id=123
  → Change to: user_id=124
  ```
* **Real Case**: Accessing someone else’s invoice, medical record, or profile by changing the `user_id`.
* **Tools**: Burp Repeater, Burp Intruder, Postman.

---

### 2. 🔐 **JWT Manipulation (Token Tampering)**

* **What It Is**: JWTs often contain encoded role or user data. Attackers try to decode, edit, and re-sign JWTs to escalate privileges.
* **Example**:

  ```json
  {
    "user": "john",
    "role": "user"
  }
  → Change to:
  {
    "user": "john",
    "role": "admin"
  }
  ```
* **Vulnerable JWT Signs**:

  * Weak or no signature validation
  * `alg: none` vulnerability
  * Predictable or leaked secret key
* **Tools**: [jwt.io](https://jwt.io), `jwt_tool`, Burp Decoder.

---

### 3. 📂 **Path Traversal / Directory Manipulation**

* **What It Is**: Changing the path to access files or directories not meant for your role.
* **Example**:

  ```
  GET /documents/own-file.pdf
  → Change to:
  /documents/../admin-file.pdf
  ```
* **Impact**: Access config files, logs, other users' files.
* **Tools**: Burp, FFUF, custom scripts.

---

### 4. 🔄 **HTTP Method Fuzzing**

* **What It Is**: Changing HTTP verbs to access unintended routes or functions (e.g., changing `GET` to `DELETE`, `POST` to `PUT`).
* **Example**:

  ```
  Original: GET /user
  Try:      DELETE /user
  ```
* **Why It Works**: Some APIs implement access control only on specific methods.
* **Tools**: Burp Repeater, `ffuf -X`, `curl`.

---

### 5. 🔁 **Session Replay**

* **What It Is**: Reusing a stolen or expired session token to see if it still grants access.
* **Real Case**: Some tokens are not properly invalidated on logout or password change.
* **Tools**: Burp Suite, Cookie Editor Extensions.

---

### 6. 🕵️‍♂️ **Discovering Hidden Admin Routes**

* **What It Is**: Guessing or brute-forcing URLs that are not linked in the UI but are still active.
* **Examples**:

  * `/admin`
  * `/superuser/panel`
  * `/user/edit?id=admin`
* **Tools**: `ffuf`, `dirsearch`, `gobuster`, Burp Content Discovery.

---

### 7. 🔢 **URL ID Swapping**

* **What It Is**: Manipulating ID in the path to view/edit/delete resources.
* **Example**:

  ```
  GET /orders/101 → Try /orders/102
  ```
* **Automate it**: Use Intruder to test many IDs (1-1000).
* **Tools**: Burp Suite, Postman.

---

### 8. 📦 **API Payload Tampering**

* **What It Is**: Editing API request bodies (JSON/XML) to change hidden fields like role, access rights, etc.
* **Example**:

  ```json
  {
    "username": "john",
    "role": "admin"
  }
  ```
* **Impact**: Elevate role, bypass validation, access restricted data.
* **Tools**: Postman, Burp, Insomnia.

---

### 9. 🧬 **Role Field Injection**

* **What It Is**: Injecting new fields into the request payload that server wasn't expecting to be user-controlled.
* **Example**:

  ```json
  {
    "user": "user123",
    "new_role": "admin"
  }
  ```
* **Where**: Especially effective during account creation or profile updates.
* **Tools**: Burp Suite, Postman.

---

## ✅ Quick Tester’s Workflow

1. 🔍 Discover endpoints with `ffuf`, `dirsearch`, or browsing app.
2. 🧪 Use Burp Repeater to test for IDOR, method changes, hidden params.
3. 🔁 Replay old sessions or tokens.
4. 📊 Log results using Burp extensions like `Autorize`.
5. 🧼 Clean logs to avoid detection (if allowed in scope).

---

## 🧠 Pro Tip

**Combine techniques** – test method tampering on hidden routes, or replay sessions with tampered JWTs for privilege escalation.

---

# ✅ **Chapter 5: Exploitation Vectors – Broken Access Control**

Broken Access Control can be **exploited through multiple vectors** depending on how the access logic is implemented (or not implemented) on the server side. Below are **advanced exploitation techniques** used by bug bounty hunters and penetration testers.

---

### 🔹 1. Force Browsing (Forced Access)

**Definition**: Accessing resources or pages by directly guessing or brute-forcing the URL, bypassing UI-based navigation restrictions.

**Example**:

* `/admin/delete_user?id=1337` is not visible to normal users, but accessible directly via browser.
* Accessing `/confidential-documents/employee_salary.pdf` even when not shown in UI.

**Tools & Techniques**:

* Use `ffuf`, `dirsearch`, or `gobuster` to brute-force paths.
* Check session roles via Burp and replay request with lower privileges.

**Real-world Case**:

* An e-commerce platform let regular users access `/admin/download-reports` via direct URL.

---

### 🔹 2. Unprotected Endpoints (API or Web)

**Definition**: APIs or web routes that are not guarded by proper access control checks (like token/role-based checks).

**Example**:

* Mobile API `/api/v1/users/all` returns all user info even for unauthenticated users.
* Endpoint `/internal/v1/settings` accessible to the public.

**Detection Tip**:

* Monitor traffic using Burp Suite Proxy while logged in as user and replay as guest.
* Intercept API calls from mobile apps using tools like **MobSF**, **Burp**, or **Frida**.

---

### 🔹 3. Weak or Missing Token Checks

**Definition**: JWT tokens or session cookies not being validated properly on the server-side.

**Example**:

* JWT token does not validate the `role` field on server, only trusts client input.

```json
{
  "user": "john",
  "role": "admin" // change this manually
}
```

**Exploitation**:

* Modify JWT tokens (using `jwt.io` or `jwt_tool`).
* Use none-algorithm (`alg: none`) or sign with weak keys like `HS256` with known secret.

**Tools**:

* [`jwt_tool`](https://github.com/ticarpi/jwt_tool)
* Burp Suite extension: JWT Editor

---

### 🔹 4. Server-Side Validation Missing or Incomplete

**Definition**: The application relies only on client-side checks (JavaScript, front-end forms) to enforce access.

**Example**:

* The frontend hides admin buttons, but the request still works when manually sent.
* No validation on request payload values such as `isAdmin: true`.

**Testing Tip**:

* Tamper with hidden fields, parameters, or form data using Burp or browser dev tools.
* Try sending crafted API requests directly via Postman or Burp Repeater.

---

### 🔹 5. Role/Privilege Field Injection

**Definition**: Adding or modifying sensitive role/privilege fields in requests that aren’t properly validated by the backend.

**Example**:

* Send `{"role":"admin"}` in a PUT/POST request to `/api/user/update`
* Add headers like `X-User-Role: admin` or parameters like `?admin=true`

**Tools**:

* Burp Suite Repeater + Intruder
* Autorize (Burp Plugin)

---

### 🔹 6. URL-Based Access Manipulation

**Definition**: Changing the numeric or string IDs in URL paths or parameters to access unauthorized data.

**Example**:

* `/profile?id=104` → change to `/profile?id=103` (IDOR)
* `/projects/marketing-team/documents` → switch `marketing-team` to `finance-team`

**Tips**:

* Look for sequential IDs.
* Check for UUIDs or slugs that can be guessed or brute-forced.

---

### 🧠 Pro Tip: Combine Techniques

Advanced attacks often **combine multiple vectors**, such as:

* JWT role tampering + unprotected API route
* Force browsing + session replay
* Parameter injection + weak server validation

---

### 📌 Checklist (Exploitation-Oriented)

| Checkpoint                                            | ✅ |
| ----------------------------------------------------- | - |
| Can I access privileged data by URL tampering?        | ⬜ |
| Does API return sensitive data without session/token? | ⬜ |
| Are tokens (JWT) vulnerable to modification?          | ⬜ |
| Is access enforced on frontend only?                  | ⬜ |
| Can I access admin APIs via forceful browsing?        | ⬜ |

---

Certainly! Here's **Chapter 6: Prevention** of Broken Access Control explained in deep and easy-to-understand format with practical insights and real-world alignment:

---

# ✅ Chapter 6: Prevention of Broken Access Control

Preventing Broken Access Control requires a **multi-layered strategy** that starts from secure coding practices to enforcing policy-based access at the server side. Here's a detailed breakdown of **all prevention techniques**, with examples and insights:

---

## 🔒 1. Enforce Role-Based Access Control (RBAC)

### What it is:

RBAC is a system where **permissions are assigned to roles**, and users are then assigned those roles.

### Example:

* `Admin` can access `/admin/deleteUser`
* `User` can access `/user/viewProfile`

### How to Implement:

* Define clear **roles** (`user`, `editor`, `admin`)
* Assign permissions to roles
* Avoid hardcoded roles in front-end

```python
# Example: Flask + Flask-Principal
@admin_permission.require(http_exception=403)
@app.route('/admin')
def admin_dashboard():
    return "Welcome Admin"
```

### Common Mistake:

Implementing role checks **only on frontend** or relying on **JWT tokens without validation on server**.

---

## 🔑 2. Deny by Default

### What it is:

By default, **block all access**, and then explicitly allow only what’s needed.

### Why?

Minimizes risk if a route or function gets exposed unintentionally.

### Example:

If a new route `/debug` is added, it should not be accessible by default unless permissions are configured.

```yaml
access_control:
  default: deny
  allow:
    - /home
    - /user/profile
```

---

## 🧾 3. Implement Audit Logs

### What it is:

Keep detailed logs of **who accessed what** and when.

### Why?

Helpful for detecting unauthorized access and analyzing breaches.

### Good Audit Log Includes:

* Timestamp
* IP address
* User ID
* Action performed
* Target resource

**Example Entry:**

```
[2025-08-01 17:32:00] - User: 104 - Action: Deleted Account ID 202 - IP: 192.168.1.12
```

### Tools:

* ELK Stack
* AWS CloudTrail
* Graylog

---

## 🔀 4. Use Randomized and Non-Guessable Object IDs

### Why?

If object IDs are sequential (e.g., `/profile/1`, `/profile/2`), it's easy for attackers to enumerate.

### Secure Practice:

Use UUIDs or hashed references:

```
/profile/f324e98a-bef2-11eb-8529-0242ac130003
```

### How?

* Replace integer-based IDs with UUIDs
* Use hash-based mapping with access control logic on server

---

## 🛡️ 5. Server-Side Authorization Only

### Key Rule:

**Never trust client-side checks** (like hidden buttons or disabled fields).

### Example:

Even if the admin panel is hidden in the UI, an attacker can directly access `/admin` via browser.

✅ Ensure all requests are validated on the backend using:

* Sessions
* Token validation
* Permission checks

---

## ⚠️ 6. Avoid Relying Solely on JWT Claims

### Risk:

Attackers can **modify JWT payload** if the token isn't signed properly or is using `alg: none`.

### Solution:

* Always validate JWT signature
* Use strong HMAC/RSA algorithms (`RS256`, `HS512`)
* Don't trust role claims without rechecking against a server-side DB

---

## 🔁 7. Use Secure Session Management

### Why?

Sessions control who you are — flaws here can allow session hijacking or privilege misuse.

### Practices:

* Use secure, HttpOnly cookies
* Rotate session tokens on login/logout
* Timeout idle sessions
* Invalidate on logout

---

## 🧪 8. Penetration Testing & Automated Scanners

Regularly scan applications using:

* **Burp Suite (Pro or Community)** with **Autorize Plugin**
* **ZAP** with access control scripts
* **OWASP Access Control Matrix**
* Role testing plugins or **custom scripts**

---

## 🧱 9. Implement Attribute-Based Access Control (ABAC)

Advanced model where access is based on attributes like:

* User’s department
* Time of access
* Resource sensitivity

### Example:

* Allow access to `/payroll` only during office hours from internal IPs for HR users.

---

## 📋 Summary Checklist for Prevention ✅

| Area                         | Check                                 |
| ---------------------------- | ------------------------------------- |
| RBAC/ABAC Implemented        | ✅ Defined roles & policies            |
| Deny-by-Default              | ✅ Unused routes blocked               |
| Random Object IDs            | ✅ No predictable patterns in URLs     |
| Logs & Monitoring            | ✅ Audit trails implemented            |
| Server-side Enforcement Only | ✅ All checks on backend               |
| JWT Signature & Role Recheck | ✅ Token validation & server-side RBAC |
| Secure Session Handling      | ✅ Idle timeout, logout invalidation   |
| Penetration Testing          | ✅ Tools like ZAP/Burp used regularly  |

---

Here’s **Chapter 7: Tools (Deep Dive)** for **Broken Access Control**:

---

## 🔧 Chapter 7: Tools – Deep Dive for Broken Access Control

These tools can help **identify**, **exploit**, and **validate** broken access control vulnerabilities across web applications and APIs.

---

### 🔹 1. **Burp Suite** (Community/Pro)

**Purpose:** Intercept requests, modify parameters, automate attacks, and perform manual testing.

**Modules to Focus:**

* **Burp Repeater:** Modify and resend requests to test access control behavior.
* **Burp Intruder:** Automate parameter fuzzing (e.g., test for IDOR or role tampering).
* **Burp Comparer:** Compare authorized vs. unauthorized responses.
* **Burp Extensions:**

  * `Autorize` – Automatically test endpoints for unauthorized access.
  * `AuthMatrix` – Role-based access testing across multiple roles.

**Example Use Case:**

* Intercept a request as an admin and replay it with a normal user's session token to see if the action is allowed.

---

### 🔹 2. **Postman**

**Purpose:** Manual API request crafting and testing.

**Use it for:**

* Sending authenticated requests with different tokens/cookies.
* Changing request headers or parameters to test access control logic.
* Scripting role-based access control tests using pre-request scripts.

**Example Use Case:**

* Send a `DELETE /users/12` request as a low-privilege user and observe if the API allows the deletion.

---

### 🔹 3. **jwt\_tool**

**Purpose:** Analyze, modify, and exploit JWTs (JSON Web Tokens).

**Functions:**

* Decode JWTs to inspect claims like `role`, `admin`, or `userid`.
* Try to change values and resign tokens (if the secret is weak or known).
* Test for “None” algorithm vulnerabilities (`alg: none`).

**Example Use Case:**

* Change `{ "role": "user" }` to `{ "role": "admin" }` and see if elevated access is granted.

---

### 🔹 4. **ffuf (Fuzz Faster U Fool)**

**Purpose:** Fuzzing paths, parameters, and endpoint discovery.

**Use it for:**

* Brute-forcing hidden directories and admin panels.
* Discovering insecure endpoints like `/admin/deleteUser`.

**Example Command:**

```bash
ffuf -u https://target.com/api/FUZZ -w common.txt -mc all
```

---

### 🔹 5. **OWASP ZAP**

**Purpose:** Open-source web application scanner.

**Features:**

* Automated scanner for access control issues.
* Manual interception similar to Burp.
* Passive scan rules for IDOR and privilege escalation patterns.

**Use Cases:**

* Spidering and fuzzing protected resources to discover misconfigurations.

---

### 🔹 6. **Autorize (Burp Extension)**

**Purpose:** Automatically test access control by replaying authorized and unauthorized requests.

**How it Works:**

* You record requests as an authenticated user.
* Autorize replays the same requests with different tokens (e.g., guest, lower-privilege).
* Flags responses where unauthorized users can access protected resources.

**Example Use Case:**

* Test 100+ API endpoints for role-based access bypass in a few minutes.

---

### 🔹 7. **Tamper Data / Modify Headers Plugins**

**Purpose:** Browser-based tools to intercept and modify requests.

**Use it for:**

* Changing cookies, headers, or tokens during browser sessions.
* Testing CORS misconfigurations or cookie-based role controls.

---

### 🔹 8. **Role Testing Scripts (Custom)**

**Idea:**

* Write Python or Bash scripts that simulate multiple users with different roles.
* Automate endpoint testing to identify inconsistent access behavior.

---

### 🧠 Pro Tip:

> Always test with multiple **roles**, **sessions**, and **unauthenticated users** to discover where **access control boundaries** fail.

---

Absolutely! Here's a **deep dive into Chapter 8: Checklist** for **Broken Access Control** — formatted, easy to understand, yet highly actionable. This checklist is designed to help you **identify and validate broken access control vulnerabilities** during penetration testing and bug bounty assessments.

---

## ✅ **Chapter 8: Broken Access Control Checklist (Deep Dive)**

---

### 🔍 **1. Can a User Access Admin-Only Features?**

**Test**:
Try accessing admin panel URLs (e.g., `/admin`, `/dashboard/admin`, `/settings/admin`) as a low-privileged user.

**How**:

* Remove or manipulate session tokens (e.g., cookies or JWTs).
* Tamper with hidden parameters like `role=admin`.
* Modify headers such as `X-User-Role`, `X-Auth`, `X-Admin`.

**Tools**:

* Burp Suite → Repeater + Intruder
* ZAP → Forced Browsing
* Postman → API fuzzing

**Payload Examples**:

```
GET /admin HTTP/1.1
Cookie: session=valid-user-session
```

➡️ **Expected**: Access Denied
➡️ **Vulnerable**: Admin panel loads for a normal user

---

### 🛠 **2. Can You Modify Your Own or Others' Roles?**

**Test**:
Try intercepting and modifying user role values in:

* Profile update requests
* Signup APIs
* PUT/PATCH/POST request bodies

**How**:

* Replace `role=user` with `role=admin`, `role=manager`, etc.
* Use alternate capitalization or obfuscation (`Role`, `admin\n`)

**Example**:

```json
PATCH /api/profile
{
  "username": "kunal",
  "role": "admin"
}
```

**Also Check**:

* WebSockets role update
* GraphQL mutation parameters

---

### 🌀 **3. Replay Session Tokens**

**Test**:
Replay the session token of another user (captured via XSS, shoulder surfing, etc.).

**Steps**:

* Log in as victim
* Copy session cookie/JWT
* Log out and use it in a different browser

**Also Try**:

* Reuse expired tokens
* Change issued timestamps (`iat`), if using JWT

**Hint**: If JWTs aren’t invalidated on logout or expiry, it’s a huge risk.

---

### 🛑 **4. Access Hidden APIs or Routes**

**Test**:
Try accessing undocumented or debug endpoints. Often exposed due to poor access control.

**Common Paths**:

* `/debug`, `/api/debug`
* `/admin/deleteAll`, `/api/users/all`
* `/internal`, `/test`, `/logs`

**Methods**:

* Use **ffuf**, **dirsearch**, **Burp Suite** (discovery plugins)

**Fuzz Headers**:

* `X-Original-URL`, `X-Forwarded-For`, `X-Rewrite-URL` can bypass path restrictions.

**Example**:

```
GET /internal/logs HTTP/1.1
X-Original-URL: /admin
```

---

### 🔗 **5. Tamper with ID/Resource Values (IDOR)**

**Test**:

* Try changing your own user ID (`/profile/102`) to someone else’s (`/profile/101`).
* Enumerate document IDs, file downloads, invoices.

**Check**:

* GET, POST, PUT, DELETE methods
* Both REST and GraphQL endpoints

**Example**:

```
GET /invoice/12345 → OK
GET /invoice/12344 → ???
```

---

### 🔃 **6. Test for Method Confusion or Tampering**

**Test**:
Try using different HTTP methods:

* PUT instead of GET
* DELETE where only GET is expected

**Try Unexpected**:

* `HEAD`, `OPTIONS`, `TRACE`, `PATCH`

**Why**: Some backend systems apply access control based on method, not the route.

---

### 🎯 **7. Bypass Access via Parameter Pollution**

**Test**:

* Duplicate keys in GET/POST:

  ```
  GET /user?role=user&role=admin
  ```
* Use encoding:

  ```
  /admin%2e%2e/user
  ```

**Payload Tips**:

* Try `?user=1` vs `?user_id=1`
* Use `URL-encoded`, `double URL-encoded`, `Base64` parameters

---

### 🔐 **8. Test for Client-Side Role Enforcement**

**Test**:

* Does the role get stored or enforced via localStorage or JS?

**Steps**:

1. Open DevTools
2. Change role in localStorage/cookies
3. Refresh

**Example**:

```js
localStorage.setItem("role", "admin")
```

**If You See Admin Features Appear** → **Bingo!**

---

### 📦 **9. Broken Object-Level Authorization (BOLA)**

**Test**:

* Can you access or delete another user's data using object references (IDs)?

**Check**:

* DELETE `/users/1234`
* GET `/messages/5678`

**Tools**:

* Burp Suite with **Autorize**
* Postman with manual IDOR testing

---

### 📋 **10. Use Automated Tools for Coverage**

**Tools**:

* 🔍 **Burp Suite** with **Autorize** and **Access Control plugin**
* ⚙️ **ffuf/dirsearch** for endpoint discovery
* 💡 **ZAP** Forced Browse & Active Scan
* 🔐 **JWT Tool** for token modification

---

## ✅ Bonus Tip: General Questions to Ask Yourself

| Question                                        | Why It Matters                             |
| ----------------------------------------------- | ------------------------------------------ |
| Can I access features meant for other roles?    | Classic vertical privilege escalation      |
| Can I access resources I don’t own?             | IDOR/BOLA                                  |
| Are access decisions enforced only client-side? | Leads to client-side bypasses              |
| Are debug/internal APIs accessible?             | Often overlooked, can be goldmines         |
| Are authorization checks consistent?            | Inconsistent checks → entry points exposed |

---


## 🧠 **Broken Access Control – Advanced Mind Map (Text Format)**

---

### 🧩 1. Introduction to Broken Access Control

* 🔐 Definition: Violation of policies that define user permissions.
* 🧨 OWASP Rank: #1 in OWASP Top 10 (most critical web security issue).
* 🔍 Impact: Unauthorized actions → data leakage, privilege abuse, account takeover.

---

### 🧪 2. Types of Broken Access Control

* 🧷 **IDOR (Insecure Direct Object Reference)**

  * Modify object IDs to access others’ data.
  * E.g., `/user/123` → change to `/user/124`.

* 🔼 **Privilege Escalation**

  * Vertical: Normal user → Admin.
  * Horizontal: Accessing peer accounts or data.

* 🛣️ **Forced Browsing**

  * Accessing hidden but unprotected paths (e.g., `/admin/panel`).

* 🔁 **Method Tampering**

  * Changing `POST` to `PUT` or `DELETE` to invoke other actions.

* 🌐 **Client-Side Enforcement**

  * Logic like "admin button" hidden in UI but not blocked on server.

* 🚫 **CORS Misconfigurations**

  * Misused `Access-Control-Allow-Origin`, leading to cross-origin access.

* 🏦 **Banking App - IDOR**

  * Real example: Transfer money between accounts by changing account IDs.

---

### 🎯 3. Real-World Scenarios

* 👤 **Profile Update → Role Escalation**

  * Users editing own profile can inject `"role": "admin"`.

* 🗑️ **Delete Any User**

  * Endpoint like `/deleteUser?id=123`, no auth check.

* 🔐 **View 2FA or Email of Others**

  * Access sensitive configurations of other users.

* 📊 **Report or Analytics Dashboard Leaks**

  * Access to `/reports?id=admin_data` without authorization.

* 📦 **Cloud APIs (e.g., S3 buckets)**

  * Exposed storage without ACL or token checks.

---

### 🧪 4. Testing Techniques

* 🧬 **Parameter Tampering**

  * Change `userID=101` → `userID=102`.

* 🔓 **JWT Manipulation**

  * Change `"role": "user"` to `"admin"` in JWT payload.

* 🛣️ **Path Traversal + Fuzzing**

  * Use `../../admin`, `../dashboard`, etc.

* ⚔️ **Method Fuzzing**

  * Try `GET`, `POST`, `PUT`, `DELETE` on same endpoint.

* 🕹️ **Session Replay**

  * Re-use cookies or tokens from previous sessions.

* 📡 **Hidden Admin Routes**

  * Try `/admin`, `/superadmin`, `/dashboard`.

* 📦 **API Payload Tampering**

  * Inject unauthorized fields in POST body like `"isAdmin": true`.

* 🎯 **Role Field Injection**

  * Add/modify JSON fields: `"role": "superadmin"`.

---

### 🚨 5. Exploitation Vectors

* 📂 **Force Browsing**

  * Directly accessing unauthorized routes or hidden pages.

* 🚪 **Unprotected Endpoints**

  * No access control checks in API or frontend.

* 🧪 **Weak Token Checks**

  * Expired tokens still work, tokens not bound to user/session.

* 📡 **No Server-side Validation**

  * Relying only on client-side UI to enforce roles.

---

### 🛡️ 6. Prevention

* 🧱 **RBAC (Role-Based Access Control)**

  * Define roles clearly and enforce them on every request.

* 🔑 **Random Object IDs**

  * Avoid incremental IDs (use UUIDs, opaque references).

* 🧾 **Audit Logs**

  * Log every access attempt, success or failure.

* 🚫 **Deny by Default**

  * Block access to everything unless explicitly allowed.

* 🔒 **Server-side Enforcement**

  * Never trust frontend. Always validate on backend.

* 📐 **Least Privilege Principle**

  * Assign minimum required rights to users/services.

---

### 🔧 7. Tools

* 🔍 **Burp Suite**

  * Intercept, modify requests, test parameter tampering.

* 🔐 **Postman**

  * Test APIs manually with crafted payloads.

* 🧬 **jwt\_tool / JWT Cracker**

  * Decode, manipulate, and test JWTs.

* 🚀 **ffuf**

  * Fuzz hidden directories or parameters.

* 🧙‍♂️ **OWASP ZAP**

  * Automated scanning for access control issues.

* 🧪 **Autorize (Burp Plugin)**

  * Detect Broken Access Control by replaying requests with lower-privilege tokens.

* ⚒️ **HackTools / Tamper Tools**

  * Browser extensions or scripts to bypass access control protections.

---

### ✅ 8. Checklist

| ✅ Test Area                          | Description                                     |
| ------------------------------------ | ----------------------------------------------- |
| 🔎 Access Admin Pages as Normal User | Check if unauthorized access is possible        |
| 🧬 Modify Role in Request            | Try `"role": "admin"` in body or JWT            |
| 🌀 Replay Expired/Old Sessions       | Test reuse of outdated tokens                   |
| 👻 Discover Hidden APIs              | Try fuzzing, guessing undocumented routes       |
| 🧰 Tamper Method                     | Switch request methods                          |
| 📥 Access Other User's Data          | IDOR checks, horizontal privilege               |
| 🚫 Disabled UI Buttons – Test Anyway | Manually trigger actions that UI restricts      |
| 🔁 Replay Requests With Role Swap    | Use lower-privilege token on higher-priv routes |
| 🔐 Force Reset Other's Credentials   | Test password/2FA reset endpoints               |

---



