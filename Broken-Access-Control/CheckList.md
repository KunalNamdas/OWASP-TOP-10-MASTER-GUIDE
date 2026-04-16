## 🔐 **Broken Access Control Checklist (Offensive Security Focused)**

> Use this checklist during reconnaissance, enumeration, exploitation, and reporting. This focuses on bypassing or manipulating access controls intentionally implemented by developers.

---

### 🧭 **1. Reconnaissance & Discovery**

* [ ] ✅ **Identify user roles and permissions** (Admin, Moderator, Basic User, Guest, etc.)
* [ ] ✅ **Crawl all endpoints** using tools like Burp Suite, OWASP ZAP, FFUF, Dirsearch.
* [ ] ✅ **Observe URL structures** that may suggest ID-based access:
  `/user/profile?id=123`, `/admin/delete?id=456`, etc.
* [ ] ✅ **Note common patterns** like `user_id`, `account_id`, `role`, `priv`, `is_admin`.

---

### 🎯 **2. Insecure Direct Object References (IDOR)**

* [ ] ✅ Test direct access to objects by changing `ID`, `username`, `account` in URLs:

  * `/invoice/123` → `/invoice/124`
  * `/user/kunal` → `/user/admin`
* [ ] ✅ Try modifying POST body parameters or JSON payloads.
* [ ] ✅ Test file access via path manipulation:
  `/files/document.pdf` → `/files/confidential.pdf`

---

### 🔄 **3. Forced Browsing (Unprotected URLs)**

* [ ] ✅ Try accessing endpoints that shouldn't be available to your role.

  * Example: `/admin/dashboard`, `/internal/settings`
* [ ] ✅ Remove UI controls (buttons/links) via dev tools and manually browse hidden functions.

---

### ⚖️ **4. Privilege Escalation / Role Manipulation**

* [ ] ✅ Test if you can escalate roles via:

  * Changing values like `"role": "user"` → `"role": "admin"`
  * Modifying cookies, JWT, or local storage items
* [ ] ✅ Observe response changes or unauthorized access

---

### 🔄 **5. Method Tampering & HTTP Verb Confusion**

* [ ] ✅ Test multiple HTTP methods: `GET`, `POST`, `PUT`, `DELETE`, `PATCH`
* [ ] ✅ Check if `DELETE /user/kunal` is blocked for user but allowed via `POST` or `PUT`
* [ ] ✅ Bypass CORS preflight using alternate verbs

---

### 🔐 **6. Bypassing Access Control Mechanisms**

* [ ] ✅ Try bypassing using:

  * Null byte injection (`%00`)
  * Encoded characters (`..%2f..`)
  * Alternate paths (`/..;/admin`)
* [ ] ✅ Remove `Authorization` header and test fallback
* [ ] ✅ Swap tokens between users

---

### 🔄 **7. Parameter Pollution / Mass Assignment**

* [ ] ✅ Test with duplicate parameters:

  * `?role=user&role=admin`
* [ ] ✅ Try adding hidden fields like `isAdmin=true` to request bodies
* [ ] ✅ Fuzz for additional parameters using a wordlist (e.g., SecLists, param-miner)

---

### 🧾 **8. JWT & Token-Based Failures**

* [ ] ✅ Modify JWT `alg` to `none` if not validated
* [ ] ✅ Bruteforce weak JWT secrets (use `jwt_tool`)
* [ ] ✅ Replay expired or revoked tokens
* [ ] ✅ Swap tokens between users (token confusion)

---

### ⛔ **9. Unvalidated Access on APIs**

* [ ] ✅ Use Postman/Burp to access internal APIs directly:

  * `/api/v1/users/delete?id=5`
* [ ] ✅ Test for unauthenticated access to admin APIs
* [ ] ✅ Try user context switching without re-authentication

---

### 💥 **10. Session Management Issues**

* [ ] ✅ Reuse cookies/session IDs across users
* [ ] ✅ Check if logout doesn't invalidate tokens
* [ ] ✅ Check for predictable or sequential session tokens

---

### 📤 **11. File Access & Functionality Abuse**

* [ ] ✅ Upload files to test override: `/uploads/image.png` → `/uploads/.htaccess`
* [ ] ✅ Download or delete files that shouldn't be accessible
* [ ] ✅ Abuse import/export features to leak data

---

### 🔁 **12. Rate Limiting Bypass**

* [ ] ✅ Bruteforce IDOR or role endpoints without triggering lockout
* [ ] ✅ Try multiple roles using automation tools like:

  * Burp Intruder
  * Turbo Intruder
  * ffuf or wfuzz

---

## 📌 Additional Tips

* 🧪 Always verify **authorization checks** are enforced **server-side**.
* 🔄 Reuse and replay traffic using **Burp Suite repeater** to test behavior.
* 🛑 Use **multiple user contexts** during testing (guest, normal, admin).
* 🧑‍💻 Use automation for role/ID fuzzing: ffuf, qsreplace, Param Miner, etc.

---

## 📋 Reporting Template Snippet (for BAC)

```markdown
**Vulnerability:** Insecure Direct Object Reference (IDOR)  
**Endpoint:** `GET /api/v1/invoice?id=112`  
**Impact:** Unauthorized access to another user’s invoice  
**Reproduction Steps:**
1. Login as user A
2. Intercept request to `/invoice?id=112`
3. Modify `id=112` → `id=113`
4. Observe that invoice of another user is accessible

**Severity:** High  
**Fix Recommendation:** Enforce server-side authorization using session or token-based access verification.
```

---

## ✅ **Broken Access Control Checklist (Offensive Security Focused) — Part 2 (13–30)**

---

### 🔁 **13. OAuth / OpenID Connect Misuse**

* [ ] ✅ Can you reuse or manipulate `access_token` or `id_token` to access other users' data?
* [ ] ✅ Is there a weak or missing audience (`aud`) validation in ID tokens?
* [ ] ✅ Is the system accepting tokens issued for other clients (confused deputy attack)?

---

### 🧱 **14. Path Traversal in Role-Relevant Endpoints**

* [ ] ✅ Try using `../../admin/config` or encoded versions (`..%2f`) to escalate access.
* [ ] ✅ Bypass file or config access checks in misconfigured servers.

---

### 👥 **15. Privilege Confusion Between Tenants (Multitenancy Issues)**

* [ ] ✅ Can one tenant access another tenant’s data by changing `tenant_id` or headers?
* [ ] ✅ Test IDOR and role escalation in multitenant setups.

---

### 🔄 **16. Hidden Features or Role Overlap**

* [ ] ✅ Test features not visible in the UI but accessible via direct API calls (e.g., `/billing/export`)
* [ ] ✅ Abuse shared endpoints where authorization logic is reused incorrectly between user types.

---

### 🚫 **17. Frontend-Enforced Access Control**

* [ ] ✅ If access control is only enforced by JavaScript logic or route guards, try bypassing by direct URL access.
* [ ] ✅ Disable JavaScript and request protected pages.

---

### 🪪 **18. Role-Based Caching Issues**

* [ ] ✅ Check if cached content is served without verifying current user permissions.
* [ ] ✅ Abuse misconfigured CDNs or proxies serving stale pages across sessions.

---

### 🧠 **19. Lack of Context-Aware Authorization**

* [ ] ✅ Try reusing valid tokens at the wrong time or wrong place (e.g., change email while unverified).
* [ ] ✅ Abuse workflows like order approvals, KYC processes, or SSO sessions.

---

### 🔂 **20. Token Scoping Misconfiguration**

* [ ] ✅ Can you access broader resources than intended using a limited-scope token?
* [ ] ✅ Are tokens missing claim-based restrictions (`scope`, `sub`, `role`, etc.)?

---

### 🧵 **21. Vertical Access Control Failures in APIs**

* [ ] ✅ Can a basic user call `/api/admin/metrics` or `/api/internal/logs`?
* [ ] ✅ Replay an admin endpoint call with a downgraded token.

---

### 🔎 **22. Horizontal Access Control via Object Guessing**

* [ ] ✅ Can you view or manipulate resources of other users via enumeration (e.g., `/profile/101`, `/profile/102`)?
* [ ] ✅ Bruteforce user IDs in POST, PUT, or DELETE calls.

---

### 🧾 **23. Insecure Workflow Transitions**

* [ ] ✅ Can you skip required steps in flows like onboarding, checkout, or KYC by directly calling the final endpoint?
* [ ] ✅ Try accessing confirmation endpoints without session state.

---

### 💬 **24. Misused Headers for Access Control**

* [ ] ✅ Modify or inject headers like `X-User-ID`, `X-Role`, or `X-Forwarded-For` to impersonate users.
* [ ] ✅ Use internal headers that upstream systems rely on for identity.

---

### 🧱 **25. CSP / CORs-Based Bypass for Access**

* [ ] ✅ Abuse misconfigured CORS (`Access-Control-Allow-Origin: *`) to hijack tokens or make authenticated requests via CSRF.

---

### 🎛️ **26. Broken Function-Level Authorization**

* [ ] ✅ Try accessing endpoints (e.g., `/delete`, `/ban`, `/assign`) that check only for authentication but not role.

---

### 🔁 **27. User Impersonation via Open Redirect or Session Injection**

* [ ] ✅ Exploit open redirects in login flows to trick the app into issuing a session for another user.
* [ ] ✅ Manipulate login tokens or SAML assertions to impersonate.

---

### 📁 **28. Unauthorized Resource Injection**

* [ ] ✅ Upload dangerous resources to shared spaces (e.g., uploading HTML to user content) to hijack sessions or admin panels.
* [ ] ✅ Abuse file names/IDs to overwrite or take control of system resources.

---

### 📂 **29. Cross-Tenant Metadata Exposure**

* [ ] ✅ Access tenant or organization-specific metadata using leaked identifiers or misconfigured endpoints (e.g., `/tenant-settings?orgId=5`).

---

### 📦 **30. Access Control via Client Trust Assumptions**

* [ ] ✅ Does the server rely solely on the client to enforce logic like user limits, cart pricing, or invoice creation?
* [ ] ✅ Modify client requests to manipulate server decisions (e.g., inject `userId` in POST).

---


