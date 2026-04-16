# 🔐 **OWASP: Broken Access Control – Full Deep Explanation**

*(With Real-World Examples + Offensive Security Checklist)*

---

## ✅ 1. **What is Broken Access Control?**

> **Access Control** = Who is allowed to do what?

**Broken Access Control** happens when:

* Users can access or modify resources they shouldn't.
* The system fails to enforce **authorization rules** at the **backend**.
* Attackers escalate privileges, access other users’ data, or perform unauthorized actions.

---

## 💣 **Why It’s So Dangerous**

* Lets attackers:

  * View other users’ personal data.
  * Act as admins.
  * Delete or modify records they don’t own.
* Often leads to **full account takeover** or **data breaches**.

🛑 Over 94% of tested apps had **some form of broken access control** (OWASP 2021).

---

## 🚨 Real-World Examples of Exploits

---

### ✅ **Example 1: Insecure Direct Object Reference (IDOR)**

**Vulnerable Request:**

```http
GET /user/profile?id=201
```

**Exploit:**
Attacker changes the ID:

```http
GET /user/profile?id=202
```

➡️ Gains access to **another user's data**.

✅ **Fix:**

* Enforce **ownership checks** server-side.
* Never trust user-controlled IDs without validation.

---

### ✅ **Example 2: Horizontal Privilege Escalation**

**Scenario:**

* Regular user accesses:

```http
POST /deleteUser?id=5
```

➡️ The endpoint doesn’t check role, so the user deletes another account.

✅ **Fix:**

* Backend must check:

```java
if (!currentUser.hasRole("ADMIN")) throw new AccessDeniedException();
```

---

### ✅ **Example 3: Vertical Privilege Escalation**

**Exploit:**

* Attacker modifies JWT:

```json
{
  "user": "normaluser",
  "role": "admin"
}
```

➡️ Admin features unlocked (because no server-side role validation).

✅ **Fix:**

* Don’t trust JWT roles without signature validation.
* Always validate **permissions server-side**.

---

### ✅ **Example 4: Admin Panel Discovery**

**Exploit:**

* Attacker guesses:

```http
GET /admin/dashboard
```

➡️ Backend has no auth check. Admin page loads.

✅ **Fix:**

* Protect sensitive routes with **strict access control**.
* Hide admin panel behind **authentication + role check**.

---

### ✅ **Example 5: Missing Function-Level Authorization**

* Buttons are hidden in UI, but endpoint is exposed:

```http
DELETE /product?id=123
```

➡️ Attacker sends the request directly via Postman.

✅ **Fix:**

* **Frontend hiding = not enough.**
* Backend must enforce permissions for every action.

---

### ✅ **Example 6: Forced Browsing**

* Attacker accesses endpoints not linked in the UI:

```http
GET /admin/users
```

➡️ If backend has no checks → full access.

✅ **Fix:**

* Use access control rules even for unused or internal URLs.

---

### ✅ **Example 7: Disabling Authorization Middleware**

**Bug:**

* A dev removes auth middleware during testing and forgets to re-enable.

✅ **Fix:**

* Use automated tests to check all endpoints are behind access control.

---

## ⚔️ Offensive Techniques to Test for Broken Access Control

| Attack                 | Technique                                               |
| ---------------------- | ------------------------------------------------------- |
| 🧪 IDOR                | Change IDs in URLs or body (`userId`, `invoiceId`)      |
| 🧪 Vertical Escalation | Modify JWTs, cookies, or headers to escalate role       |
| 🧪 Endpoint Access     | Try accessing hidden or admin-only endpoints            |
| 🧪 Function Access     | Call functions via API even if hidden in UI             |
| 🧪 URL Guessing        | Fuzz paths like `/admin`, `/config`, `/delete`, `/edit` |
| 🧪 Replay              | Replay requests from another user’s session             |
| 🧪 Header Injection    | Add headers like `X-User-ID` or `X-Admin: true`         |

---

## 🧰 Prevention Techniques

| Fix                                                                   | Description |
| --------------------------------------------------------------------- | ----------- |
| ✅ Enforce access control **on the backend**                           |             |
| ✅ Check **ownership** of resources (user owns what they're accessing) |             |
| ✅ Use **secure frameworks** like Spring Security, Express middleware  |             |
| ✅ Apply **least privilege** principle                                 |             |
| ✅ Log **unauthorized access attempts**                                |             |
| ✅ Avoid relying solely on JWT, cookies, or hidden fields              |             |
| ✅ Use **ACLs or RBAC/ABAC models** for role management                |             |

---

## 🧾 Offensive Security Checklist – Broken Access Control

| #                                             | Check                                                                 | ✅ |
| --------------------------------------------- | --------------------------------------------------------------------- | - |
| ☐                                             | Try accessing other users’ resources by changing IDs (IDOR)           |   |
| ☐                                             | Try admin endpoints as a normal user (`/admin`, `/delete`, `/config`) |   |
| ☐                                             | Modify JWT claims (`role`, `isAdmin`) and test access                 |   |
| ☐                                             | Use intercepted admin requests and replay them with another account   |   |
| ☐                                             | Test if deleting/updating other users’ data is possible               |   |
| ☐                                             | Try calling hidden backend APIs directly (bypass UI)                  |   |
| ☐                                             | Check if functionality depends only on UI (hidden buttons ≠ security) |   |
| ☐                                             | Fuzz URLs with common admin paths (`/admin`, `/staff`, `/internal`)   |   |
| ☐                                             | Test if backend validates **both role and ownership**                 |   |
| ☐                                             | Check for missing access control on POST/PUT/DELETE, not just GET     |   |
| ☐                                             | Try unauthorized role change via API or registration                  |   |
| ☐                                             | Bypass access control via alternate methods:                          |   |
|   - Case change (`/Admin`)                    |                                                                       |   |
|   - Encoded URL (`%2Fadmin`)                  |                                                                       |   |
|   - Method change (e.g., GET instead of POST) |                                                                       |   |
| ☐                                             | Scan for forgotten debug/test endpoints                               |   |
| ☐                                             | Check for "soft deletes" you can reverse as a user                    |   |
| ☐                                             | Test for access after logout / session expiry                         |   |

---

## 🔒 Tools to Help You Exploit/Test

| Tool                             | Use                                  |
| -------------------------------- | ------------------------------------ |
| 🛠️ **Burp Suite**               | Modify, replay requests, tamper IDs  |
| 🛠️ **OWASP ZAP**                | Scan for IDOR, access control issues |
| 🛠️ **Postman / Insomnia**       | Manually test API permissions        |
| 🛠️ **JWT.io**                   | Decode/forge JWTs                    |
| 🛠️ **Ffuf / DirBuster**         | Discover hidden endpoints            |
| 🛠️ **AuthMatrix (Burp Plugin)** | Map roles vs. functionality          |

---

## 🧠 Developer Mindset for Prevention

* Don’t assume the UI protects you — **trust nothing from the client**.
* Always check:

  > ❓ Is the user **authenticated**?
  > ❓ Does the user have **permission** for this action?
  > ❓ Does the user **own** this resource?

---

## 🔐 Final Summary

| Broken Access Control Type        | Example                         | Risk                        |
| --------------------------------- | ------------------------------- | --------------------------- |
| IDOR (Insecure Direct Object Ref) | Change `userId=2`               | Data leakage / manipulation |
| Missing Role Check                | Admin endpoint exposed to user  | Privilege escalation        |
| UI Only Protection                | Hidden buttons                  | Full function abuse         |
| Forced Browsing                   | Direct access to unlinked pages | Unauthorized access         |
| JWT / Cookie Forgery              | Modify `role=admin`             | Full control of app         |
| No Ownership Validation           | Delete others’ orders           | Data loss / fraud           |

---

## 📘 Want a PDF or Checklist Template?

I can give you:

* ✅ Downloadable PDF version of this guide
* ✅ Offensive/Broken Access Control Testing Worksheet
* ✅ Practice vulnerable web app for IDOR & privilege escalation
* ✅ Pre-built Burp Suite testing templates

