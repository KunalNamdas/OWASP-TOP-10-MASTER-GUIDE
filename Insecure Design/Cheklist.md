## ✅ **Insecure Design Checklist (Offensive Security Focused)**

### 🔍 1. **Authentication and Authorization Design**

* [ ] Are there **design flaws** in how user sessions are created, validated, and terminated?
* [ ] Does the app **mix authentication and authorization logic**?
* [ ] Can you escalate privileges by **tampering with roles or access levels** in tokens, headers, or requests?
* [ ] Are there **no defined security requirements** in design documents?
* [ ] Is **role-based access control (RBAC)** missing or poorly implemented?

> 🛠 Try manipulating session tokens, JWTs, or user roles directly in requests.

---

### 🔐 2. **Trust Boundary Mismanagement**

* [ ] Are **internal APIs exposed** to untrusted environments?
* [ ] Is **client-side input** trusted by backend processes without validation?
* [ ] Can you **forge internal requests** (SSRF, Insecure Direct Object References)?
* [ ] Is there a **lack of segmentation between services** (e.g., frontend/backend)?

> 🛠 Attempt lateral movement by accessing endpoints outside your scope.

---

### 📉 3. **Lack of Secure Development Principles**

* [ ] Are security best practices (e.g., principle of least privilege, fail-safe defaults) ignored in the code logic?
* [ ] Are security controls added as an afterthought instead of baked into design?
* [ ] Does the application **lack threat modeling documentation**?
* [ ] Is input validation **not centralized**, making bypass easier?

> 🛠 Check for inconsistent validation logic or insecure libraries across modules.

---

### 🔄 4. **Improper State Machine Logic**

* [ ] Can the application be forced into an **invalid state** through crafted sequences (e.g., skipping payment steps)?
* [ ] Are there **missing or flawed preconditions** in business logic flow?
* [ ] Are unauthorized actions **allowed by default** under certain conditions?

> 🛠 Fuzz for state transitions, bypassing logical flow using tools like Burp Suite’s Repeater and Sequencer.

---

### 📦 5. **Missing Design for Threat Scenarios**

* [ ] Does the system lack **countermeasures** for common threat scenarios (e.g., replay attacks, race conditions)?
* [ ] Are **rate-limiting**, **retries**, and **timeouts** handled poorly or missing?
* [ ] Is **logging and alerting** for anomalous behavior absent?

> 🛠 Attempt brute force, DoS, or abuse flows with automated tools.

---

### 📤 6. **Client-Side Trust Assumptions**

* [ ] Are **critical decisions made on the client side** (e.g., pricing, logic enforcement)?
* [ ] Can you manipulate **client-side JavaScript or mobile apps** to gain unauthorized features?
* [ ] Is **debug information** left in production JS files or APKs?

> 🛠 Reverse engineer or tamper with client apps/scripts using tools like Frida, MobSF, or browser dev tools.

---

### 🛡️ 7. **Inadequate Cryptographic Design**

* [ ] Is encryption handled manually without using **proven libraries**?
* [ ] Is there **no separation between encrypted and non-encrypted data flows**?
* [ ] Are keys **hardcoded** or **not rotated**?

> 🛠 Try leaking keys, weak encryption modes, or decrypting data.

---

### 🕵️ 8. **Security Features Disabled for UX**

* [ ] Are important security features like 2FA, CSRF protection, etc. **disabled or made optional**?
* [ ] Is **error handling** verbose, revealing internal design or stack traces?

> 🛠 Trigger validation and error messages to reveal backend structure.

---

### ⚠️ 9. **Improper Use of Third-Party Components**

* [ ] Are third-party libraries used without **security vetting**?
* [ ] Are outdated or **vulnerable dependencies present**?
* [ ] Is open-source software used with **insecure default configurations**?

> 🛠 Run tools like Trivy or Syft to identify vulnerable components.

---

### 🔐 10. **Checklist for Exploitation Paths (Red Team View)**

* [ ] Can you **simulate logical flaws** (e.g., ordering free items, skipping approvals)?
* [ ] Can you escalate to **admin-level or system-level** via design gaps?
* [ ] Can you **bypass business rules** or process flows?
* [ ] Can you identify **data exposure paths** via poor architecture?

---

## 📌 Bonus Offensive Tools for Insecure Design Testing:

* **Burp Suite Pro** – for fuzzing flows and analyzing request/response chains.
* **OWASP ZAP** – automated scanning and logic flaw testing.
* **Postman** – simulate crafted API flows.
* **Mitmproxy** – analyze and manipulate traffic between components.
* **Threat Dragon / IriusRisk** – visualize and exploit threat models.

---


### ✅ **Insecure Design Checklist (Offensive Security Focused)**

📍\*\*(Part 2: Points 11–30)\*\*

---

### 🔄 **11. Business Logic Abuse Vectors**

* [ ] Can you bypass or abuse business rules (e.g., get discounts, skip payments)?
* [ ] Are workflow steps enforceable only by the client (not server)?
* [ ] Is there no check on action dependencies (e.g., confirm email before purchase)?

> 🛠️ Use Burp Repeater and logic manipulation to disrupt process flows.

---

### 🕳️ **12. Lack of Defense-in-Depth**

* [ ] Are security controls **not layered** across front-end, API, and database?
* [ ] Does the application rely **solely on client-side validation**?
* [ ] Are APIs assuming authentication is already validated elsewhere?

> 🛠 Bypass front-end validation and hit APIs directly to check for enforcement gaps.

---

### 🧱 **13. Missing Threat Modeling for Critical Flows**

* [ ] Are **high-risk features** like password reset, payments, and user invite systems not threat modeled?
* [ ] Are **business-critical endpoints** lacking abuse protection?

> 🛠 Fuzz key flows like checkout, referrals, and token reset URLs.

---

### 🗺️ **14. No Isolation for Admin or Privileged Interfaces**

* [ ] Are admin routes (`/admin`, `/staff`) **publicly accessible**?
* [ ] Is there **no additional security layer** (IP whitelisting, MFA) for sensitive panels?
* [ ] Are debug routes exposed in production?

> 🛠 Test for low-privilege access to restricted functions or dashboards.

---

### ⚙️ **15. Misuse of HTTP Methods & REST Verbs**

* [ ] Are insecure methods like `PUT`, `DELETE`, or `OPTIONS` allowed for all users?
* [ ] Can state-changing operations be triggered via `GET` requests?
* [ ] Is `PATCH` allowed without proper validation?

> 🛠 Use tools like `curl`, `httpx`, or Burp to manipulate verbs and force unexpected behaviors.

---

### 🧬 **16. Improper Multi-Tenancy Design**

* [ ] Can users access or manipulate data belonging to **other tenants or organizations**?
* [ ] Are tenant IDs predictable or passed via insecure parameters?
* [ ] Is authorization **only handled at the front-end**?

> 🛠 Try IDOR-style attacks across user/tenant boundaries.

---

### 🧯 **17. Missing Abuse Prevention Design**

* [ ] Are there **no limits** on registration, login, or API usage?
* [ ] Are user actions (e.g., password reset, report abuse) **not rate-limited**?
* [ ] Can you spam or DoS certain flows?

> 🛠 Use `ffuf`, `intruder`, or custom scripts for rate and spam testing.

---

### 🔑 **18. Static, Guessable, or Reusable Tokens**

* [ ] Are password reset tokens, email verification codes **guessable or reused**?
* [ ] Are token lengths short or generated via predictable algorithms?
* [ ] Is token rotation missing after first use?

> 🛠 Attempt brute force or reuse expired links to test validity.

---

### 📉 **19. Weak State Transition Validation**

* [ ] Can you jump from `unverified` to `verified` by changing status parameters?
* [ ] Are users allowed to skip mandatory onboarding or payment steps?
* [ ] Are `state` or `step` parameters enforced client-side?

> 🛠 Intercept requests and skip steps to test state machine flaws.

---

### 🗃️ **20. Overtrust in File Metadata or MIME Types**

* [ ] Is file validation **based only on extension or MIME header**?
* [ ] Can users upload `.php`, `.jsp`, or `.exe` files with spoofed content-type?
* [ ] Is there no deep file content inspection?

> 🛠 Upload test payloads like polyglots, XSS in SVG, or Web Shell stagers.

---

### 🧩 **21. Confused Deputy Problems in Architecture**

* [ ] Are services performing actions **on behalf of unauthenticated or lower-priv users**?
* [ ] Is API access delegated without verifying caller authority?

> 🛠 Trigger backend to make privileged API calls for you (SSRF, broken delegation).

---

### 📜 **22. Insecure Design of Notifications or Messaging Systems**

* [ ] Can users **spoof notifications or system messages** to other users?
* [ ] Is the notification logic **triggered client-side or misvalidated**?

> 🛠 Inject messages, simulate admin actions, or spam inboxes to test.

---

### 🧼 **23. Missing Design for Input Canonicalization**

* [ ] Are inputs not **normalized** before being processed (e.g., whitespace, encoding tricks)?
* [ ] Can you bypass filters using **Unicode, full-width characters, or hex**?

> 🛠 Use obfuscated payloads and encodings to slip past logic checks.

---

### 🔧 **24. Improper Feature Flag Implementation**

* [ ] Are **in-development features** hidden client-side but active server-side?
* [ ] Are feature flags **controlled by client parameters or cookies**?

> 🛠 Try enabling premium/admin features by toggling front-end flags.

---

### 📍 **25. Lack of Architectural Redundancy or Fail-Safes**

* [ ] Is there a **single point of failure** in critical authentication or payment flow?
* [ ] Does the application fail **open** instead of securely?

> 🛠 Induce errors and crash flows to see how gracefully (or insecurely) they recover.

---

### 🧠 **26. Insecure ML/AI Logic Design**

* [ ] Are models vulnerable to **input poisoning**, **model inversion**, or **prompt injection**?
* [ ] Are user inputs directly fed into decision-making algorithms without sanitation?

> 🛠 Attempt prompt injection, adversarial inputs, or logic flipping.

---

### 🎯 **27. Misuse of Client-Controlled Parameters**

* [ ] Are sensitive decisions based on client-controlled fields (e.g., `is_admin=true`)?
* [ ] Are discount, pricing, or permission parameters trusted from frontend?

> 🛠 Modify hidden fields or request payloads for privilege escalation.

---

### 📤 **28. Insecure Data Flow Between Microservices**

* [ ] Are internal service calls **not authenticated or signed**?
* [ ] Is input from one service passed to another **without validation**?
* [ ] Is message queue traffic **unencrypted or unauthenticated**?

> 🛠 Look for injection, tampering, or unauthorized access between services.

---

### 🧱 **29. Improper Handling of Legacy Endpoints**

* [ ] Are old endpoints still active and vulnerable to known exploits?
* [ ] Is version control absent, allowing fallback to `/v1` or `/beta` APIs?

> 🛠 Bruteforce endpoint paths (`/api/v0`, `/legacy`, `/old-api`) to find overlooked surfaces.

---

### 🎛️ **30. Insecure User-Controlled Configuration or Customization**

* [ ] Can users inject insecure configurations (e.g., HTML themes, plugin paths)?
* [ ] Are customizable features stored without sanitation or validation?

> 🛠 Try injecting paths, XSS, or template injection in user-defined settings.

---



