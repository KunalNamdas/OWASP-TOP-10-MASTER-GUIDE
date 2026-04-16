## 🔐 Chapter 1: Cryptographic Failures — Deep Dive

---

### 🧠 **Definition**

Cryptographic Failures (previously known as *Sensitive Data Exposure* in OWASP Top 10 2017) refers to failures related to cryptography — whether it's **missing encryption**, **weak encryption**, **misconfigured algorithms**, or **poor key management** — that lead to **sensitive data being exposed**.

---

### ⚠️ **Why It Matters**

Improper use or complete absence of encryption can result in the **exposure of sensitive data** such as:

* Passwords
* Credit card numbers
* Session tokens
* Personal Identifiable Information (PII)
* Health records

Attackers can:

* Intercept data in transit (Man-in-the-Middle)
* Steal or manipulate stored data
* Replay tokens or credentials
* Impersonate users or decrypt sensitive info

---

### 🔓 **Common Root Causes**

* Data not encrypted at rest or in transit
* Weak or outdated encryption algorithms (e.g., MD5, SHA1)
* Hardcoded or exposed keys
* Improper key rotation or lifecycle management
* No TLS or improperly configured TLS
* Insecure randomness sources

---

### 📌 **Sensitive Data Examples**

* Authentication credentials (username/password)
* Payment information (card numbers, CVV)
* Session identifiers or cookies
* Personal data (name, email, phone)
* Government-issued IDs
* Financial or health records

---

### 🔐 **Key Security Goals of Cryptography**

| Goal            | Description                                       |
| --------------- | ------------------------------------------------- |
| Confidentiality | Ensure data is not readable to unauthorized users |
| Integrity       | Ensure data is not altered in transit or storage  |
| Authenticity    | Ensure the sender of data is verified             |
| Non-repudiation | Sender cannot deny sending the message later      |

---

### 🔄 **Attack Scenarios**

| Scenario                   | Description                                          |
| -------------------------- | ---------------------------------------------------- |
| MITM Attacks               | Intercepting unencrypted HTTP traffic                |
| Database Dump              | Sensitive info stored in plaintext                   |
| JWT None Attack            | JWT signed with “none” algorithm                     |
| TLS Downgrade              | Forced downgrade to weak SSL version                 |
| Key Leakage                | API keys, private keys exposed in GitHub or frontend |
| Predictable Random Numbers | Poorly generated tokens/session IDs                  |

---

## 🔐 Chapter 2: Types of Cryptographic Failures (Deep)

Understanding the **types** of cryptographic failures helps in identifying **how and where** things can go wrong. Each failure is categorized based on misuse, absence, or weakness of cryptographic mechanisms.

---

### 1. ❌ **Data Not Encrypted (In Transit or At Rest)**

* **In Transit:** Data sent over HTTP instead of HTTPS, no TLS.
* **At Rest:** Databases, backups, or logs store PII/passwords in plaintext.

📌 **Real-world Example:**
Login form sends credentials via HTTP. An attacker with MITM capabilities can steal credentials.

---

### 2. 🧪 **Weak or Deprecated Cryptographic Algorithms**

Using outdated or easily breakable algorithms:

* MD5, SHA1 (for hashing)
* DES, RC4, or ECB mode in AES
* SSLv2/v3, TLS 1.0

📌 **Why Bad:** These algorithms are vulnerable to **collision**, **padding oracle**, and **brute-force** attacks.

---

### 3. 🔑 **Hardcoded or Exposed Secrets**

Secrets like:

* API keys
* Encryption keys
* JWT secret
* DB credentials

...are embedded in:

* JavaScript code
* Mobile apps (APK)
* GitHub repositories

📌 **Result:** Anyone can extract these and decrypt or impersonate.

---

### 4. 🕳️ **Improper Key Management**

Failures include:

* Storing keys unencrypted
* No key rotation
* Reusing keys across services
* No key expiry
* Keys stored alongside encrypted data

📌 **Impact:** Even strong encryption can be broken if keys are mishandled.

---

### 5. 🧊 **No Salting or Weak Salting of Passwords**

Storing passwords as:

* Plaintext
* Base64 encoded
* Weakly hashed (`MD5("password")`)
* Without salt or with predictable salt

📌 **Attackers Can Use:**

* Rainbow tables
* Precomputed hash dictionaries

---

### 6. 📦 **Insecure Use of Cryptographic Libraries**

* DIY crypto instead of proven libraries
* Custom encryption algorithms
* Misconfigured libraries (e.g., setting AES to ECB mode)

📌 **Security Rule:** **Don’t roll your own crypto.**

---

### 7. 🔁 **Lack of Integrity Checks**

* Missing HMAC or signature in sensitive payloads (JWTs, cookies)
* Attacker can tamper data

📌 **Example:** JWT tokens with `alg=none`, allowing the attacker to modify payload.

---

### 8. 🛠️ **Broken or Misconfigured TLS/SSL**

* Using expired/self-signed certificates
* Disabling cert validation
* Accepting all SSL certs in mobile apps (`OkHttpClient.Builder().hostnameVerifier(...)`)
* Using TLS 1.0 or older

📌 **Impact:** Opens door to MITM attacks.

---

### 9. 🔂 **Predictable or Weak Random Number Generation**

* Using predictable seeds (`time()`, `PID`)
* Poor PRNG (`Math.random()` for crypto)

📌 **Effect:** Attacker can predict tokens, OTPs, session IDs, leading to impersonation or hijacking.

---

### 10. 🔓 **Failure to Encrypt Sensitive Data in Backups or Logs**

* Log files containing passwords or tokens
* Backups stored on cloud buckets without encryption or access controls

📌 **Example:** Leaked S3 backups containing user data.

---

### 11. 🪪 **JWT Misuse or Misconfiguration**

* No expiration (`exp`)
* Using symmetric keys in shared systems
* Accepting unsigned tokens (`alg=none`)
* Weak secret (e.g., `"123456"`)

📌 **Exploit:** Attacker forges JWT and accesses protected resources.

---

### 12. 🧾 **Plaintext Configuration Files**

* `.env` files with secrets committed to Git
* YAML, XML config files not encrypted

📌 **Security Tip:** Use secret managers like **HashiCorp Vault**, **AWS Secrets Manager**.

---

### Summary Table

| Type                     | Risk Example         |
| ------------------------ | -------------------- |
| No Encryption            | HTTP login           |
| Weak Algorithms          | MD5 password hash    |
| Exposed Secrets          | API key in frontend  |
| Poor Key Management      | Unrotated key        |
| No Salt in Password Hash | Rainbow table attack |
| DIY Crypto               | Easy to break        |
| Broken TLS               | MITM attack          |
| Predictable Random       | OTP hijack           |
| Leaky Logs               | Password in logs     |
| Misused JWT              | Forged auth          |
| Plaintext Configs        | Secrets in Git       |

---


## 🔐 Chapter 3: Real-World Scenarios of Cryptographic Failures (Deep Dive)

---

These examples show how cryptographic misconfigurations and weak practices have led to real-world breaches, making it easier to understand the impact and risk:

---

### 🔹 Scenario 1: **Plaintext Password Storage**

* **Context:** A website stores user passwords in plaintext in its database.
* **Impact:** If the database is breached, attackers gain immediate access to all accounts.
* **Example Breach:** Yahoo's 2013 breach exposed over 3 billion user accounts with improperly secured credentials.
* **Detection:** Inspect database dumps or response content; use Burp to test password recovery/email confirmation flows.

---

### 🔹 Scenario 2: **Using Weak Hash Algorithms (MD5/SHA1)**

* **Context:** Application hashes passwords using MD5.
* **Impact:** These are fast algorithms and easily crackable with rainbow tables or hashcat.
* **Example:** LinkedIn breach in 2012 where 117 million passwords hashed with SHA1 were cracked.
* **Detection:** Analyze password reset responses or source code (if open); test hash output formats.

---

### 🔹 Scenario 3: **Lack of TLS on Login Pages**

* **Context:** Login page sends credentials over HTTP instead of HTTPS.
* **Impact:** Anyone on the same network can sniff traffic and capture credentials (Man-in-the-Middle).
* **Example:** Starbucks' app (in earlier versions) transmitted sensitive data over unencrypted channels.
* **Detection:** Intercept traffic using Wireshark or Burp; look for `http://` endpoints in login flows.

---

### 🔹 Scenario 4: **Hardcoded Secrets in Code**

* **Context:** Developers hardcode API keys, encryption keys, or passwords in public repositories.
* **Impact:** Attackers scrape public GitHub repos to collect secrets.
* **Example:** Thousands of hardcoded AWS keys found across GitHub via automated scripts.
* **Detection:** Use tools like TruffleHog, GitLeaks to scan for secrets in code repos.

---

### 🔹 Scenario 5: **Improper Certificate Validation**

* **Context:** Mobile app does not verify SSL certificates properly.
* **Impact:** Opens doors to MITM attacks with self-signed certificates.
* **Example:** Apps vulnerable to MITM because they accepted all SSL certificates without validation.
* **Detection:** Use tools like MobSF or test with self-signed certificates during traffic interception.

---

### 🔹 Scenario 6: **Broken or Predictable Encryption**

* **Context:** App encrypts data using ECB mode (Electronic Codebook).
* **Impact:** Patterns in encrypted data leak information, allowing statistical analysis.
* **Example:** Adobe’s 2013 breach exposed encrypted passwords with patterns clearly visible.
* **Detection:** Inspect cipher mode and padding; check libraries/configs used.

---

### 🔹 Scenario 7: **Missing Integrity Checks (No MAC)**

* **Context:** App encrypts messages but doesn’t validate integrity using HMAC or similar.
* **Impact:** Attacker can tamper with encrypted data, and the app will still process it.
* **Example:** Vulnerabilities in some JWT implementations lacking verification.
* **Detection:** Test encrypted data tampering and monitor server behavior.

---

### 🔹 Scenario 8: **Reuse of Nonces or IVs**

* **Context:** App uses the same IV (Initialization Vector) for each encryption.
* **Impact:** Allows attackers to infer patterns or even recover plaintext.
* **Detection:** Monitor multiple encrypted messages and check for IV reuse patterns.

---

## 🔍 Chapter 4: Testing Techniques (Cryptographic Failures)

---

### 🔸 1. **Static Code Analysis**

**Goal**: Identify insecure cryptographic implementations in source code
**How to Test**:

* Scan for use of outdated or broken algorithms (e.g., `MD5`, `SHA1`, `DES`)
* Identify hardcoded keys, salts, or secrets
* Check improper use of cryptographic libraries (e.g., insecure modes like ECB)
  **Tools**:
* **Semgrep**
* **SonarQube**
* **Bandit** (for Python)
* **FindSecBugs** (Java)

---

### 🔸 2. **Dynamic Analysis**

**Goal**: Observe crypto operations at runtime to spot weaknesses
**How to Test**:

* Intercept encrypted traffic using proxies (Burp, ZAP)
* Inspect cookies and session tokens for weak entropy or predictability
* Check TLS/SSL versions during handshakes (vulnerable to downgrade attacks)
  **Tools**:
* **Burp Suite**
* **ZAP Proxy**
* **Wireshark**

---

### 🔸 3. **TLS/SSL Configuration Testing**

**Goal**: Test transport-level encryption effectiveness
**How to Test**:

* Verify strong ciphers are enabled (disable weak ones like RC4)
* Check for secure TLS versions (avoid SSLv2/3, TLS 1.0/1.1)
* Ensure HTTP Strict Transport Security (HSTS) is in place
  **Tools**:
* **SSL Labs by Qualys**
* **testssl.sh**
* **OpenSSL** (e.g., `openssl s_client -connect`)

---

### 🔸 4. **Token/Hash Analysis**

**Goal**: Validate strength and unpredictability of hashes and tokens
**How to Test**:

* Brute-force or rainbow table attacks on captured hash values
* Analyze session tokens for entropy (repeatability, length, structure)
* Check use of salts in hashing functions
  **Tools**:
* **Hashcat**
* **John the Ripper**
* **Burp Sequencer**

---

### 🔸 5. **Man-in-the-Middle (MITM) Testing**

**Goal**: Simulate interception of encrypted data
**How to Test**:

* Use MITM proxy tools to capture encrypted credentials or tokens
* Test if certificate pinning is enforced
* Check if application accepts self-signed certificates (a red flag)
  **Tools**:
* **mitmproxy**
* **Burp Suite**
* **Frida** (for bypassing pinning on mobile)

---

### 🔸 6. **Replay and Integrity Testing**

**Goal**: Test if encrypted messages can be replayed or altered
**How to Test**:

* Replay encrypted requests and observe server behavior
* Modify encrypted parameters to test for padding oracle attacks or lack of MAC
  **Techniques**:
* Timing analysis for padding oracle attacks
* Inspect for lack of integrity checks (MAC missing)

---

### 🔸 7. **Fuzzing Encrypted Interfaces**

**Goal**: Stress the cryptographic components
**How to Test**:

* Input malformed data into encryption/decryption fields
* Observe for crashes, timeouts, or predictable behaviors
  **Tools**:
* **zzuf**
* **boofuzz**
* Custom scripts

---

### 🔸 8. **File & Data Inspection**

**Goal**: Discover plaintext sensitive data due to encryption failures
**How to Test**:

* Analyze configuration files, logs, and databases for unencrypted credentials
* Check backups and memory dumps
  **Tools**:
* **grep**, **strings**
* **Binwalk**
* Manual review

---

## 🔥 Chapter 5: Exploitation Vectors – Cryptographic Failures

Understanding how attackers exploit weak cryptography is key to building effective defenses. Below are the main exploitation vectors used when cryptographic failures are present in web applications and APIs.

---

### 🔸 1. **Intercepting Unencrypted Communications (Man-in-the-Middle - MITM)**

**Description**:
If HTTPS is not enforced, or if weak SSL/TLS configurations are in place, attackers can intercept, modify, or replay traffic between client and server.

**Attack Scenario**:

* User logs in on a public Wi-Fi using `http://example.com/login`.
* Attacker on the same network sniffs traffic and captures credentials in plaintext.

**Tools**: Wireshark, mitmproxy, Burp Suite with SSL stripping

**Fix**:

* Enforce HTTPS everywhere with HSTS.
* Disable weak ciphers and TLS versions.

---

### 🔸 2. **Exploiting Poor Key Management**

**Description**:
Improper key generation, storage, or rotation allows attackers to recover or guess encryption keys.

**Attack Scenario**:

* Hardcoded keys found in mobile apps or JavaScript files.
* Keys reused across environments (test/dev/prod) allow access from less secure systems.

**Tools**: Strings, Jadx, GitHub Dorking, TruffleHog

**Fix**:

* Use KMS (Key Management Systems).
* Rotate keys regularly.
* Never store keys in client-side code.

---

### 🔸 3. **Cracking Weak Hashes or Encryption Algorithms**

**Description**:
Use of outdated or weak hashing/encryption algorithms like MD5, SHA1, or DES enables brute-force or dictionary attacks.

**Attack Scenario**:

* Passwords hashed with MD5 are leaked.
* Attacker runs rainbow table attacks to reverse hashes.

**Tools**: Hashcat, John the Ripper, CrackStation, Hydra

**Fix**:

* Use strong algorithms like SHA-256 (with salt), bcrypt, or Argon2.
* Add salt and peppering techniques for extra security.

---

### 🔸 4. **Padding Oracle Attacks**

**Description**:
Occurs when attackers can decrypt ciphertext byte-by-byte using error responses from the server related to incorrect padding.

**Attack Scenario**:

* CBC mode encryption used without proper error handling leaks padding errors.
* Attacker manipulates the ciphertext and observes error messages to reconstruct plaintext.

**Tools**: PadBuster, custom scripts

**Fix**:

* Use authenticated encryption modes like AES-GCM.
* Do not leak error messages related to decryption.

---

### 🔸 5. **JWT (JSON Web Token) Exploits**

**Description**:
Improper validation or implementation of JWT tokens can allow signature bypass or token tampering.

**Attack Scenario**:

* `alg` header changed from `HS256` to `none`, server accepts token without verification.
* Public key replaced with attacker’s own in RS256 → HS256 downgrade.

**Tools**: jwt\_tool, Burp Suite, JWT.io debugger

**Fix**:

* Always validate `alg` value.
* Avoid using `none` algorithm.
* Keep keys private and secure.

---

### 🔸 6. **Exposing Sensitive Data in Logs or URLs**

**Description**:
Sensitive encrypted or plaintext data might be exposed through logs, URLs, or error messages.

**Attack Scenario**:

* Password reset tokens or API keys logged in server logs.
* Encrypted credentials passed as URL parameters.

**Tools**: Log inspection, proxy tools, grep, ELK Stack

**Fix**:

* Mask sensitive data in logs.
* Avoid passing secrets in URLs.
* Use POST body instead.

---

### 🔸 7. **Insecure Storage of Encrypted Data**

**Description**:
Even encrypted data is insecure if stored improperly—like storing encryption keys alongside encrypted data.

**Attack Scenario**:

* Database backup contains both encrypted data and keys in environment files.
* Attacker exfiltrates everything and decrypts locally.

**Fix**:

* Separate encrypted data and keys.
* Use encrypted volumes and secure storage like AWS KMS, Vault, or HSM.

---

### 🔸 8. **Replay Attacks**

**Description**:
Attackers capture encrypted requests and replay them if nonce/timestamp is not properly used.

**Attack Scenario**:

* Mobile app sends the same encrypted payload each time without a nonce.
* Attacker captures and replays to perform repeated unauthorized actions.

**Fix**:

* Use timestamps and nonces.
* Reject old or duplicate requests.

---

### 🔸 9. **Client-Side Decryption/Encryption**

**Description**:
Encryption/decryption logic on the client side exposes secrets to attackers.

**Attack Scenario**:

* JavaScript decrypts sensitive content using hardcoded key.
* Attacker reverse-engineers the logic and decrypts all data.

**Fix**:

* Perform encryption/decryption only on the server.
* Avoid exposing crypto logic in the frontend.

---

### 🔸 10. **Brute-Forcing Encrypted Data (Low Entropy)**

**Description**:
Encryption is useless if data has predictable patterns or low entropy (e.g., 4-digit PINs).

**Attack Scenario**:

* Encrypted credit card PINs (0000–9999) allow brute-force decryption.

**Fix**:

* Use sufficient entropy.
* Implement brute-force protection and monitoring.

---

## 🔐 **Chapter 6: Prevention (Deep)**

**Objective:** Understand how to **prevent cryptographic failures** by following best practices and secure design patterns.

---

### ✅ **1. Use Strong and Approved Algorithms Only**

* **Use industry-standard encryption algorithms:**

  * AES-256 for symmetric encryption
  * RSA 2048/4096 for asymmetric encryption
  * ECDSA / Ed25519 for digital signatures
* **Never use outdated or insecure algorithms** like:

  * MD5, SHA-1 (Prone to collision attacks)
  * RC4, DES, 3DES
* Refer to **NIST**, **OWASP**, or **CIS Benchmarks** for guidance.

---

### ✅ **2. Enforce HTTPS Everywhere**

* Use **TLS 1.2 or TLS 1.3 only**.
* Ensure **HSTS (HTTP Strict Transport Security)** is enabled.
* Redirect all HTTP traffic to HTTPS.
* Use valid certificates (e.g., from Let’s Encrypt).

---

### ✅ **3. Secure Key and Secret Management**

* Never hard-code secrets in:

  * Source code
  * `.env` files exposed in builds
* Use secret managers:

  * **AWS Secrets Manager**
  * **HashiCorp Vault**
  * **Azure Key Vault**
* Regularly rotate keys/secrets and use **least privilege** access.

---

### ✅ **4. Use Proper Key Lengths and Expiry**

* AES: 128/256-bit keys
* RSA: Minimum 2048 bits (preferably 4096)
* Set **key expiry dates** and rotate keys frequently.
* Implement **automatic revocation** mechanisms.

---

### ✅ **5. Implement Strong Hashing for Passwords**

* Use **bcrypt**, **scrypt**, or **Argon2** with salt.
* Never use MD5 or SHA1 for password storage.
* Configure cost factors (e.g., `bcrypt(cost=12)`).

---

### ✅ **6. Use Secure Random Number Generators**

* Use `os.urandom()` or `secrets` module in Python.
* Avoid `random` for cryptographic operations.
* In Java, use `SecureRandom`.

---

### ✅ **7. Apply Integrity Checks**

* Implement HMAC or Digital Signatures to:

  * Verify data integrity and authenticity.
* Avoid trusting unauthenticated ciphertext.

---

### ✅ **8. Validate Certificates**

* Ensure the app **verifies server SSL certificates**:

  * Avoid using `verify=False` in requests
  * Perform **certificate pinning** where possible (e.g., mobile apps).

---

### ✅ **9. Encrypt Sensitive Data at Rest and in Transit**

* Files, databases, and backups should be encrypted.
* Use **Field-level encryption** for highly sensitive fields (e.g., SSNs).
* Ensure S3 buckets, database volumes, and local filesystems are encrypted.

---

### ✅ **10. Prevent Crypto Misconfigurations**

* Validate cipher modes:

  * Use **AES-GCM or AES-CBC with random IVs**.
* Avoid ECB (Electronic Codebook) mode at all costs.
* Ensure **padding and IVs** are correctly handled and not reused.

---

## 🧰 **Chapter 7: Tools (Deep Dive)**

**Objective:** Equip yourself with the most powerful and relevant tools used for **detecting**, **testing**, and **preventing** cryptographic failures in web and mobile apps, APIs, and infrastructure.

---

### 🔍 **1. Manual Testing Tools for Crypto Failures**

| Tool         | Purpose                                        | Usage Example                                         |
| ------------ | ---------------------------------------------- | ----------------------------------------------------- |
| `Burp Suite` | Intercept traffic, test SSL/TLS, hash leakages | Test HTTPS misconfigurations, tamper encrypted tokens |
| `Postman`    | Test API endpoints manually                    | Send JWTs, encrypted parameters                       |
| `CyberChef`  | Analyze and decode hashes/encryption           | Decrypt base64, analyze JWTs, test HMACs              |
| `Insomnia`   | Like Postman, useful for testing HTTPS + JWT   | Validate HTTPS headers, test SSL certs                |

---

### 🧪 **2. Automated Scanners**

| Tool                        | Strengths                                           | Command/Usage                                      |
| --------------------------- | --------------------------------------------------- | -------------------------------------------------- |
| `testssl.sh`                | Checks SSL/TLS configurations & vulnerabilities     | `./testssl.sh https://target.com`                  |
| `sslscan`                   | Quickly enumerates supported SSL ciphers            | `sslscan target.com`                               |
| `Nikto`                     | Detects SSL and HTTP misconfigs                     | `nikto -h https://target.com`                      |
| `sslyze`                    | Deep SSL/TLS scanning for cipher and protocol flaws | `sslyze --regular target.com`                      |
| `Nmap` + `ssl-enum-ciphers` | Check weak SSL ciphers with Nmap                    | `nmap --script ssl-enum-ciphers -p 443 target.com` |

---

### 🔓 **3. Token & JWT Analysis**

| Tool                | Purpose                                     | Usage                                       |
| ------------------- | ------------------------------------------- | ------------------------------------------- |
| `jwt.io`            | Decode and verify JWT tokens                | Online UI                                   |
| `jwt_tool.py`       | Exploit and test JWTs (none alg, weak keys) | `python3 jwt_tool.py -t <token>`            |
| `JWC` (JWT Cracker) | Bruteforce JWT secret keys                  | `python3 jwc.py -t <token> -d wordlist.txt` |

---

### 🧮 **4. Hash & Encoding Tools**

| Tool              | Purpose                            | Usage                                  |
| ----------------- | ---------------------------------- | -------------------------------------- |
| `Hash-Identifier` | Detect hash types                  | `python3 hashid.py <hash>`             |
| `Hashcat`         | Crack weak hashes using GPU        | `hashcat -m 0 hashes.txt wordlist.txt` |
| `John the Ripper` | Brute-force common hashes          | `john --format=raw-md5 hashes.txt`     |
| `CrackStation`    | Online hash cracking (limited)     | Web UI                                 |
| `CyberChef`       | Encode/Decode, AES, JWT, XOR, HMAC | Web UI                                 |

---

### 🔐 **5. Secret Detection & Management Tools**

| Tool                  | Purpose                                 | Usage                                      |
| --------------------- | --------------------------------------- | ------------------------------------------ |
| `TruffleHog`          | Detects secrets and keys in Git repos   | `trufflehog --regex --entropy=True <repo>` |
| `Gitleaks`            | Detects hardcoded secrets in repos      | `gitleaks detect --source=. `              |
| `GitRob`              | Audit GitHub repos for exposed secrets  | GitHub-linked                              |
| `AWS Secrets Manager` | Secure storage and retrieval of secrets | IAM-based access                           |
| `HashiCorp Vault`     | Centralized secret management           | REST API or CLI                            |

---

### 🧬 **6. Static and Dynamic Analysis Tools**

| Tool                             | Description                                    | Use Case                          |
| -------------------------------- | ---------------------------------------------- | --------------------------------- |
| `SonarQube`                      | Scan codebases for crypto flaws                | Java, Python, JS projects         |
| `Bandit`                         | Python static analyzer for crypto/API flaws    | `bandit -r project/`              |
| `MobSF`                          | Mobile Security Framework (Android/iOS)        | Detect insecure crypto in APK/IPA |
| `FindSecBugs`                    | Java static code analyzer                      | Run in CI pipelines               |
| `AppScan`, `Fortify`, `Veracode` | Enterprise-grade crypto static/dynamic testing | Web & mobile app audits           |

---

### 🧱 **7. Certificate & Key Analysis Tools**

| Tool        | Purpose                                    | Example                                  |
| ----------- | ------------------------------------------ | ---------------------------------------- |
| `openssl`   | Inspect, generate, and validate certs/keys | `openssl x509 -in cert.pem -text -noout` |
| `cfssl`     | Manage and validate TLS certs              | Build internal PKI                       |
| `x509lint`  | Lint and validate certificates             | `x509lint mycert.pem`                    |
| `mitmproxy` | Analyze TLS traffic and certificate issues | Capture and inspect mobile/web traffic   |

---

### 🛡️ **8. Monitoring & Hardening Tools**

| Tool            | Description                                     | Usage                 |
| --------------- | ----------------------------------------------- | --------------------- |
| `Lynis`         | System audit tool with crypto tests             | `lynis audit system`  |
| `OSQuery`       | Query endpoints for SSL, secrets, certs         | SQL-based             |
| `Falco`         | Detect abnormal crypto library usage (runtime)  | K8s-based             |
| `Sysdig Secure` | Monitor crypto lib misuses in containers        | Cloud-native security |
| `AWS Inspector` | Detects crypto weaknesses in AWS infrastructure | AWS-only              |

---

### 🧠 **Pro Tips for Tool Usage in Real-World Pentests**

* 🔁 Combine `Burp` + `jwt_tool.py` + `CyberChef` to exploit token flaws.
* 🛡️ Run `testssl.sh`, `nmap`, and `sslscan` on all exposed services during recon.
* ⚠️ Use `Hashcat` only on hashes you legally obtained during testing.
* 📦 Use `TruffleHog` in your CI/CD pipeline to stop secrets from leaking into Git.

---

## 📘 Chapter 8: Reporting & Documentation (Deep)

---

### 🔹 Why Reporting Matters

Proper documentation of cryptographic failures ensures that:

* Security teams can understand the **root cause and impact**.
* Developers can take **corrective actions**.
* Regulatory requirements (like **GDPR**, **HIPAA**) are fulfilled.
* Recurrent issues are **tracked and mitigated** over time.

---

### 🔹 What to Include in the Report

| Section                            | Description                                                                                                                     |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| 🔍 **Vulnerability Summary**       | Concise explanation of the cryptographic flaw. Example: "Sensitive user passwords are stored using MD5 hashing without a salt." |
| 📌 **Location/Endpoint**           | API route, function, or system component where the failure was discovered.                                                      |
| 🛠 **Technical Details**           | Hashing/encryption algorithms used, key size, implementation flaws, libraries involved.                                         |
| 🚨 **Impact Analysis**             | Describe what an attacker could achieve: credential theft, data breach, privacy violation, etc.                                 |
| 🧪 **Testing Approach**            | Tools and manual methods used to discover the issue.                                                                            |
| 🧬 **Proof of Concept (PoC)**      | Step-by-step exploit showing the vulnerability’s presence (without causing damage).                                             |
| ✅ **Recommendations**              | Remediation steps: adopt secure algorithms, increase key length, enable HTTPS, etc.                                             |
| 🔁 **References**                  | OWASP, NIST, RFC standards, CWE-ID links.                                                                                       |
| 🗂 **Evidence (Screenshots/Logs)** | Capture of intercepted traffic, decrypted data, tool outputs.                                                                   |

---

### 🔹 Documentation Tools

* **Markdown / AsciiDoc**: Clean, developer-friendly reporting.
* **OWASP DefectDojo**: Vulnerability management platform.
* **Dradis**: Used for structured security assessments.
* **Git (Private Repo)**: Version control for internal audit reports.
* **Screenshots with Annotations**: Tools like Greenshot or Lightshot.
* **PDF Reporting**: Final formal delivery for clients/stakeholders.

---

### 🔹 Compliance Considerations

| Regulation | Documentation Requirements                                                     |
| ---------- | ------------------------------------------------------------------------------ |
| GDPR       | Encryption status of personal data must be logged and auditable.               |
| HIPAA      | Requires risk analysis documentation and remediation proof.                    |
| PCI DSS    | Mandates use of strong cryptography and documentation of encryption practices. |

---

### 🔹 Best Practices

* 📂 Organize reports by **vulnerability category** and **business impact**.
* ⏳ Timestamp all logs, evidence, and test results.
* 🔐 Redact any sensitive data from the report (e.g., real passwords or keys).
* 🔁 Maintain a version history of reports for audit purposes.
* ✅ Include a **"Verified Fix"** section after retesting.

---

### 🔹 Sample Summary Snippet

> **Vulnerability:** AES Encryption used with ECB mode for credit card data
> **Impact:** Allows pattern recognition and partial data leakage
> **Evidence:** Encrypted identical blocks seen in intercepted traffic
> **Remediation:** Switch to AES-GCM or AES-CBC with IV

---





