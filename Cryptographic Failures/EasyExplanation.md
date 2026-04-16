# 🔐 **OWASP: Cryptographic Failures – Full Deep Explanation**

*(+ Real Examples & Offensive Pentesting Checklist)*

---

## ✅ 1. **What Are Cryptographic Failures?**

> These are flaws in how your app **protects sensitive data** using cryptography.

It’s **not just bad encryption** — it includes:

* **No encryption** where needed.
* **Weak or outdated algorithms.**
* **Improper key storage.**
* **Leaking secrets in code or logs.**

---

## 💣 **Why It's Dangerous**

* Attackers can:

  * Steal passwords, tokens, credit cards, PII.
  * Decrypt sensitive data.
  * Forge sessions or escalate privileges.
* Causes **massive compliance violations**: GDPR, HIPAA, PCI-DSS.

---

## 🚨 Real-World Exploits and Examples

---

### ✅ Example 1: Data Stored Without Encryption

**Scenario:**

* User passwords or credit cards stored as plaintext in DB.

**Exploit:**

* Attacker gains DB access → reads sensitive data instantly.

✅ **Fix:**

* Use **bcrypt** or **argon2** for passwords.
* Use **AES-GCM** with secure key storage for sensitive fields.

---

### ✅ Example 2: Weak Password Hashing (e.g., MD5/SHA1)

**Scenario:**

```plaintext
Stored password = SHA1(password)
```

**Exploit:**

* Attacker gets hash → uses rainbow tables to reverse it.

✅ **Fix:**

* Never use SHA1/MD5.
* Use adaptive algorithms like:

  * `bcrypt`
  * `argon2id`
  * `PBKDF2`

---

### ✅ Example 3: Hardcoded Secrets in Code

**Scenario:**

```javascript
const apiKey = "sk_test_hardcodedapikey";
```

**Exploit:**

* Code leaked on GitHub → attacker gets access to APIs/services.

✅ **Fix:**

* Store secrets in **environment variables** or secret managers.
* Use `.gitignore` to prevent accidental leaks.

---

### ✅ Example 4: Missing HTTPS (TLS)

**Scenario:**

* App allows login over plain HTTP.

**Exploit:**

* Attacker sniffs network (e.g., Wi-Fi) → steals passwords and tokens.

✅ **Fix:**

* Force HTTPS for **all pages**.
* Use **HSTS headers** to prevent protocol downgrade.

---

### ✅ Example 5: Insecure Random Number Generation

**Scenario:**

```javascript
let token = Math.random().toString(36).substr(2);
```

**Exploit:**

* Math.random is **predictable** → token forgery possible.

✅ **Fix:**

* Use secure generators:

  * `crypto.randomBytes()` in Node.js
  * `SecureRandom` in Java
  * `crypto.getRandomValues()` in browser

---

### ✅ Example 6: Using ECB Mode for Encryption

**Scenario:**

```java
Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
```

**Exploit:**

* ECB leaks patterns (blocks encrypted independently) → attacker sees structure.

✅ **Fix:**

* Use **AES-GCM**, **AES-CBC with IV**, or **authenticated encryption**.

---

### ✅ Example 7: Exposing Secrets in Logs or URLs

**Scenario:**

```http
GET /reset?token=abcdefgh12345678
```

* Token shows up in logs, browser history.

✅ **Fix:**

* Use POST for sensitive data like tokens.
* Avoid logging secrets or set up **log scrubbing**.

---

### ✅ Example 8: No Key Rotation or Expiry

**Scenario:**

* API keys valid forever, never rotated.

**Exploit:**

* Once leaked, attacker uses them **indefinitely**.

✅ **Fix:**

* Enforce **short-lived tokens** (JWTs, access keys).
* Set **key rotation policies**.

---

### ✅ Example 9: Broken JWT Signatures

**Scenario:**

* JWT is accepted with `alg: none`.

**Exploit:**

* Attacker forges valid token without a signature.

✅ **Fix:**

* Reject unsigned tokens.
* Always verify JWTs using secret/public key.
* Allow only safe algorithms (`HS256`, `RS256`).

---

## 🧰 Prevention Techniques (Developer Fixes)

| Fix                                                              | Description |
| ---------------------------------------------------------------- | ----------- |
| ✅ Use **modern, strong algorithms** (AES-GCM, bcrypt, RSA-2048+) |             |
| ✅ Encrypt all sensitive data **at rest and in transit**          |             |
| ✅ Use **TLS 1.2 or 1.3 only** — disable older SSL/TLS versions   |             |
| ✅ Never store secrets in code or Git — use env vars, vaults      |             |
| ✅ Never log passwords, tokens, or encryption keys                |             |
| ✅ Use **secure random number generators**                        |             |
| ✅ Rotate keys and secrets regularly                              |             |
| ✅ Use HSTS headers: `Strict-Transport-Security`                  |             |
| ✅ Enforce secure password hashing with `bcrypt`, `argon2`, etc.  |             |
| ✅ Validate JWT tokens properly and never allow `alg: none`       |             |

---

## ⚔️ Offensive Pentesting Checklist — Cryptographic Failures

| # | Test                                                                | ✅ |
| - | ------------------------------------------------------------------- | - |
| ☐ | Check if passwords are stored in plaintext or with MD5/SHA1         |   |
| ☐ | Check if site allows HTTP access (no redirect to HTTPS)             |   |
| ☐ | Use Wireshark to sniff login credentials (if no HTTPS)              |   |
| ☐ | Scan repo/codebase for hardcoded secrets (use TruffleHog, GitLeaks) |   |
| ☐ | Check if JWT accepts `alg: none` or weak algorithms                 |   |
| ☐ | Check for reuse of reset tokens or predictable token generation     |   |
| ☐ | View logs and error messages for secret leaks                       |   |
| ☐ | Check if encryption uses ECB or no IV                               |   |
| ☐ | Test random number generator output (repeated tokens?)              |   |
| ☐ | Check if sensitive data (card, address, PII) is encrypted in DB     |   |
| ☐ | Analyze TLS setup: outdated protocols, weak ciphers                 |   |
| ☐ | Look for secrets in GET requests, headers, or referrers             |   |
| ☐ | Look for long-lived JWTs or unrotated API keys                      |   |

---

## 🔎 Tools to Find Cryptographic Failures

| Tool                          | Use                                |
| ----------------------------- | ---------------------------------- |
| 🔐 **Wireshark**              | Sniff HTTP traffic for credentials |
| 🔍 **SSL Labs Test**          | Analyze HTTPS/TLS configuration    |
| 🕵️ **GitLeaks / TruffleHog** | Scan code repos for secrets        |
| 🛠️ **JWT.io / Burp Suite**   | Test JWT tokens for weaknesses     |
| 🔒 **CyberChef**              | Analyze encoded/encrypted data     |
| 🔁 **hashcat**                | Brute force weak hashes            |
| 🧪 **Nmap + NSE scripts**     | Scan TLS versions, ciphers         |

---

## 🧠 Secure Developer Mindset

* Ask:

  > 🔐 Is this data **sensitive**?
  > 🔐 Is it **encrypted** in storage and in transit?
  > 🔐 Are my secrets **stored safely** and rotated?
  > 🔐 Is my crypto **modern and tested**?

* Never build your own encryption.

* Always follow best-practice libraries and frameworks.

---

## 🔐 Final Summary Table

| Cryptographic Failure | Example                     | Fix                                |
| --------------------- | --------------------------- | ---------------------------------- |
| Plaintext storage     | Passwords in DB unencrypted | Use bcrypt/argon2                  |
| Weak crypto           | SHA1, ECB mode              | Use AES-GCM, PBKDF2                |
| Leaked secrets        | Keys in code or logs        | Use env vars, secret vaults        |
| Broken HTTPS          | Allowing HTTP               | Enforce TLS + HSTS                 |
| Predictable tokens    | `Math.random()`             | Use `crypto.randomBytes()`         |
| JWT issues            | `alg: none`, weak signature | Enforce `HS256`, validate properly |

---


