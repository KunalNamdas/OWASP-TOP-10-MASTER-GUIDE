### ✅ **Cryptographic Failures Checklist (Offensive Security Focused)**

#### 🔐 **1. TLS/SSL Misconfigurations**

* [ ] Is the application using outdated or vulnerable protocols (e.g., SSL 2.0, SSL 3.0, TLS 1.0/1.1)?
* [ ] Are weak ciphers or cipher suites like RC4, DES, or 3DES in use?
* [ ] Is there lack of Perfect Forward Secrecy (PFS)?
* [ ] Is the certificate self-signed or expired?
* [ ] Does the server support insecure renegotiation?
* [ ] Is HSTS (HTTP Strict Transport Security) missing?

#### 🔑 **2. Insecure Storage or Transmission of Sensitive Data**

* [ ] Is sensitive data (e.g., passwords, credit card info) being transmitted over plain HTTP?
* [ ] Is sensitive data being stored without encryption at rest?
* [ ] Are hardcoded encryption keys or credentials found in code?
* [ ] Is sensitive data being cached, logged, or stored in URL parameters?

#### 🧪 **3. Weak Encryption Algorithms**

* [ ] Are deprecated algorithms like MD5, SHA-1, RC4 used?
* [ ] Is ECB mode used in block ciphers instead of CBC/GCM?
* [ ] Are weak or predictable IVs and nonces being used?
* [ ] Is custom or homegrown encryption implemented?

#### 🔄 **4. Key Management Failures**

* [ ] Are encryption keys stored in code or config files?
* [ ] Is there no key rotation mechanism in place?
* [ ] Are the same keys used across multiple environments (dev, prod)?
* [ ] Is the key length insufficient (e.g., AES-128 when AES-256 is feasible)?

#### 🧍 **5. Improper Password Hashing**

* [ ] Are passwords stored using unsalted hashes?
* [ ] Is a fast hashing algorithm (e.g., MD5, SHA1, SHA256) used instead of bcrypt, Argon2, or PBKDF2?
* [ ] Is a static salt or no salt used at all?
* [ ] Is password stretching missing?

#### 🔓 **6. Missing or Weak Authentication Tokens**

* [ ] Are JWTs signed with none algorithm or symmetric key exposed?
* [ ] Is there lack of token expiration or rotation?
* [ ] Are tokens stored in insecure places (e.g., localStorage)?

#### 📡 **7. Lack of Cryptographic Verification**

* [ ] Is digital signature verification missing or improperly implemented?
* [ ] Is the integrity of downloaded files/packages unverified (e.g., no checksums)?

#### 🏗️ **8. Insecure Random Number Generation**

* [ ] Is `rand()` or insecure PRNG used instead of `cryptographically secure PRNG`?
* [ ] Are predictable or sequential token values observed?

#### 🚫 **9. Broken or Missing Encryption**

* [ ] Is encryption advertised but not actually implemented?
* [ ] Is there sensitive data leaking via side channels (e.g., timing, error messages)?
* [ ] Are backups encrypted?

#### 📋 **10. Server & Infrastructure Configuration**

* [ ] Is there a weak SSH configuration (e.g., outdated ciphers)?
* [ ] Are credentials and private keys exposed via Git history or open directories?
* [ ] Is TLS not enforced via redirection or HSTS headers?

---

### ✅ **Cryptographic Failures Checklist (Offensive Security Focused)**

#### 📍 **11–30. Advanced & Offensive-Oriented Checks**

#### ⚠️ **11. Misuse of Public Key Infrastructure (PKI)**

* [ ] Are expired or revoked certificates still accepted?
* [ ] Is certificate pinning absent or improperly configured?
* [ ] Is mutual TLS (mTLS) missing where appropriate (e.g., internal services)?

#### 🛑 **12. No Certificate Transparency Monitoring**

* [ ] Is the domain not monitored in CT logs, exposing it to unnoticed rogue certificates?

#### 🔁 **13. Replay Attacks Not Prevented**

* [ ] Are cryptographic tokens or requests replayable due to lack of nonce/timestamp?

#### 🕵️ **14. No Cryptographic Logging or Audit Trail**

* [ ] Are encryption operations (e.g., decrypts/failures) unlogged, hindering forensic analysis?

#### 🧬 **15. Use of Static Initialization Vectors (IVs)**

* [ ] Is the same IV reused across multiple encryption operations?

#### 🧊 **16. Compression Side-Channel Exposure (e.g., CRIME/BREACH)**

* [ ] Is HTTP compression enabled over encrypted channels without proper mitigations?

#### ⚙️ **17. Improper Token Construction**

* [ ] Are access tokens predictable or based on timestamp/username without randomness?

#### 🔧 **18. Misconfigured or Open KMS (Key Management System)**

* [ ] Can attacker enumerate or access keys due to overly permissive IAM roles?

#### 🧱 **19. Legacy Systems with Weak Defaults**

* [ ] Do older systems (e.g., Java 6 apps) use weak defaults like `SunJCE` with low entropy?

#### 🧼 **20. Improper Use of XOR or Obfuscation Instead of Real Encryption**

* [ ] Is XOR or Base64 being misused as a substitute for cryptographic protection?

---

#### 📉 **21. Decryption Oracle Present**

* [ ] Can attackers interact with a decryption endpoint to reveal plaintext through error messages?

#### 🧪 **22. Padding Oracle Vulnerabilities**

* [ ] Is there observable behavior difference in padding errors during decryption (e.g., CBC-Padding Oracle)?

#### 🧨 **23. Client-Side Encryption Fully Trusted**

* [ ] Is sensitive encryption performed only on client-side with no server-side verification?

#### 🪪 **24. Public/Private Key Disclosure**

* [ ] Are public/private key pairs accessible in open-source repos or `.git` folders?

#### 🧬 **25. Hybrid Encryption Misimplementation**

* [ ] Are symmetric keys exposed due to improper RSA/AES hybrid usage?

#### 🔄 **26. Lack of Forward Secrecy in Messaging Protocols**

* [ ] Do messaging systems (e.g., chats) allow historical message decryption after key compromise?

#### 🧯 **27. Improper Revocation of Compromised Keys**

* [ ] Is there no revocation mechanism for leaked/rotated keys (e.g., not removing old JWT secrets)?

#### 🕳️ **28. Use of Weak Key Derivation Functions**

* [ ] Are insecure functions like `SHA1(password)` used instead of PBKDF2, bcrypt, or scrypt?

#### 📊 **29. No Entropy Validation**

* [ ] Are cryptographic operations based on weak randomness sources (e.g., predictable entropy pools)?

#### 📂 **30. Backup Keys and Secrets Exposed**

* [ ] Are old or rotated encryption keys still stored or backed up in unprotected formats?

---


