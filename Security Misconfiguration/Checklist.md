### ✅ **Security Misconfiguration Checklist (Offensive Security Focused)**

#### 🖥️ **1. Web Server & HTTP Header Misconfigurations**

* [ ] Is the server exposing version information (e.g., Apache/2.4.49)?
* [ ] Are unnecessary HTTP methods (e.g., PUT, DELETE, TRACE) enabled?
* [ ] Is directory listing enabled on web directories?
* [ ] Are sensitive files (e.g., `.git`, `.env`, `backup.zip`, `config.old`) accessible?
* [ ] Are security headers missing or misconfigured?

  * [ ] `Content-Security-Policy`
  * [ ] `Strict-Transport-Security`
  * [ ] `X-Content-Type-Options`
  * [ ] `X-Frame-Options`
  * [ ] `Referrer-Policy`
  * [ ] `Permissions-Policy`

#### 📦 **2. Unnecessary Features, Services, or Ports**

* [ ] Are unused ports open (e.g., 21/FTP, 23/Telnet, 3306/MySQL)?
* [ ] Are debug interfaces or endpoints accessible (e.g., `/debug`, `/admin`)?
* [ ] Is default content or setup/config page exposed (e.g., `/phpinfo`, `/setup.php`)?
* [ ] Are staging, test, or backup environments live and accessible?

#### 🔐 **3. Default Credentials & Weak Access Control**

* [ ] Are default usernames/passwords still enabled (e.g., admin/admin)?
* [ ] Are public cloud instances (S3, GCS, Azure) misconfigured and publicly accessible?
* [ ] Are privileged endpoints accessible without authentication?
* [ ] Is there improper access control on sensitive APIs or routes?

#### 🏗️ **4. Application Platform Misconfiguration**

* [ ] Is detailed error output enabled (e.g., stack traces, database errors)?
* [ ] Are verbose exception messages or debug logs shown to users?
* [ ] Are framework default routes, pages, or debug modes enabled?
* [ ] Are CMS admin panels (e.g., WordPress `/wp-admin`) unprotected?

#### 🧪 **5. Misconfigured File Permissions & Deployment**

* [ ] Are file/folder permissions too permissive (e.g., 777)?
* [ ] Is `.git` or `.svn` folder accessible publicly?
* [ ] Is sensitive data accidentally pushed to VCS (GitHub)?
* [ ] Are hardcoded secrets, tokens, or keys exposed in source code?

#### 🔄 **6. Misconfigured API & Microservices**

* [ ] Are APIs lacking authentication and rate limiting?
* [ ] Is GraphQL introspection exposed in production?
* [ ] Are debug, test, or internal APIs deployed in production?
* [ ] Are excessive privileges granted to services or API keys?

#### 🏢 **7. Server Infrastructure & Cloud Misconfiguration**

* [ ] Are public cloud buckets/objects readable/writable without auth?
* [ ] Are cloud metadata services (e.g., AWS `169.254.169.254`) exposed?
* [ ] Is infrastructure-as-code (Terraform, Ansible) stored with sensitive info?
* [ ] Is Kubernetes dashboard unauthenticated or overly permissive?

#### 🛡️ **8. Security Control Misconfigurations**

* [ ] Are WAF/IDS/IPS disabled or bypassable?
* [ ] Are logs or alerts not being generated for suspicious activity?
* [ ] Are DDoS protections missing or disabled?
* [ ] Are malware scans on uploaded files missing?

#### 🧍 **9. Authentication & Session Misconfigurations**

* [ ] Is MFA/2FA not enforced for critical actions or admin accounts?
* [ ] Are session timeouts too long or nonexistent?
* [ ] Is session ID predictable or not rotated after login?
* [ ] Are session cookies lacking `HttpOnly`, `Secure`, `SameSite` flags?

#### 🧠 **10. Misconfigured Security Policies or Lack of Hardening**

* [ ] Is SELinux/AppArmor disabled or in permissive mode?
* [ ] Is password policy too weak (e.g., allows `123456`)?
* [ ] Are software and packages outdated or not patched?
* [ ] Are Docker containers running as root?

---

### 🔍 Bonus: Offensive Enumeration Tips for Security Misconfigurations

| Area              | Tool / Technique                                     |
| ----------------- | ---------------------------------------------------- |
| HTTP Methods      | `curl -X OPTIONS` / `nmap --script http-methods`     |
| Directory Listing | `dirsearch`, `ffuf`, `gobuster`, browser             |
| Headers           | `curl -I`, `httpx`, `nmap --script http-headers`     |
| Cloud Buckets     | `aws s3 ls`, `gcp_bucket_enum`, `Slurp`, `S3Scanner` |
| VCS Exposure      | Append `/.git/HEAD`, check `.svn/entries`            |
| Port Scanning     | `nmap -p- -sV`, `rustscan`, `masscan`                |
| Error Handling    | Manipulate input to trigger error (e.g., `' OR 1=1`) |
| CMS Misconfigs    | `wpscan`, `droopescan`, custom fuzzers               |

---

Absolutely! Below are **20 additional in-depth checklist points** for **Security Misconfiguration (Offensive Security Focused)** — expanding your arsenal beyond basics to advanced and overlooked vectors:

---

### 🧨 **11. Web Application Firewall (WAF) & CDN Bypass**

* [ ] Is the WAF disabled or misconfigured?
* [ ] Can WAF/CDN be bypassed using IPs, headers (`X-Forwarded-For`), or payload obfuscation?
* [ ] Is origin IP exposed (via DNS history, `curl`, or `host`) allowing direct attack?

---

### 🕸️ **12. Misconfigured CORS Policies**

* [ ] Is `Access-Control-Allow-Origin` set to `*` with `Allow-Credentials`?
* [ ] Are subdomains or wildcard origins improperly trusted?
* [ ] Is CORS preflight validation missing or improperly handled?

---

### 💬 **13. Verbose Error Messages in APIs or Backends**

* [ ] Do API responses expose stack traces or internal path structures?
* [ ] Are internal service names, IPs, or technologies leaked via errors?

---

### 🧾 **14. Improper Logging Practices**

* [ ] Are sensitive details (passwords, tokens, headers) written to logs?
* [ ] Are logs accessible from the web (e.g., `/logs/`, `.log` files)?

---

### 🧱 **15. Container & Docker Misconfigurations**

* [ ] Is Docker API exposed without authentication?
* [ ] Are containers running as root?
* [ ] Are sensitive environment variables hardcoded in Dockerfiles?

---

### 🗝️ **16. Misconfigured Secrets Management**

* [ ] Are secrets stored in `.env` files exposed via misconfigured paths?
* [ ] Are secrets hardcoded in JavaScript files, client-side code, or mobile apps?
* [ ] Are API keys or tokens embedded in source maps (`.map` files)?

---

### 🏷️ **17. Debug & Stack Trace Information**

* [ ] Is detailed debug output (e.g., `DEBUG=True`) visible in production?
* [ ] Is exception handling missing in critical application logic?

---

### 📲 **18. Mobile & Client-Side Misconfigurations**

* [ ] Are mobile apps using insecure API endpoints?
* [ ] Is certificate pinning absent or bypassable?
* [ ] Are client-side storage (localStorage/SharedPreferences) storing sensitive data?

---

### 🏗️ **19. CI/CD & DevOps Exposure**

* [ ] Are Jenkins, GitLab CI, or Travis exposed without authentication?
* [ ] Are environment variables exposed in build logs or pipelines?
* [ ] Are CI/CD webhooks misconfigured, allowing unauthorized triggers?

---

### 📤 **20. Misconfigured Email Infrastructure**

* [ ] Are SPF, DKIM, or DMARC records missing or weak?
* [ ] Can spoofed emails be sent from trusted domains?
* [ ] Are bounce-back or misrouted email vulnerabilities present?

---

### 🕵️‍♂️ **21. Public Recon Artifacts**

* [ ] Is sensitive information exposed via GitHub/GitLab leaks?
* [ ] Are cloud storage URLs shared on public paste sites or forums?

---

### 🧩 **22. SRI (Subresource Integrity) Misconfigurations**

* [ ] Are external JS/CSS resources loaded without SRI hashes?
* [ ] Is `integrity` attribute missing from critical CDN imports?

---

### 🌐 **23. Improper Redirection or Open Redirects**

* [ ] Are unvalidated user-controlled redirects possible?
* [ ] Is `redirect_uri` in OAuth flows vulnerable?

---

### 🔒 **24. Broken TLS Implementation (App Layer)**

* [ ] Is the app forcing mixed content (HTTP inside HTTPS)?
* [ ] Are TLS certs mismatched or serving wrong hostnames?

---

### 🗃️ **25. Misconfigured Backup and Snapshot Exposure**

* [ ] Are backup files publicly available (e.g., `.tar.gz`, `.sql`, `.bak`)?
* [ ] Are virtual machine snapshots exposed (e.g., `.vmdk`, `.ova`)?

---

### ⛓️ **26. Misconfigured Content Delivery Networks**

* [ ] Is CDN caching sensitive data (e.g., authenticated user responses)?
* [ ] Is CDN not configured to purge stale content securely?

---

### 📡 **27. SSRF Risk via Metadata APIs or Misrouted Requests**

* [ ] Can internal services be reached via SSRF?
* [ ] Are metadata services (`169.254.169.254`) accessible?

---

### 🔁 **28. Misconfigured HTTP Redirects**

* [ ] Are redirects occurring without HTTPS enforcement?
* [ ] Is it possible to downgrade secure requests via redirect logic?

---

### 🧱 **29. Firewall, ACL, or Security Group Misconfiguration**

* [ ] Are databases or management interfaces (e.g., MongoDB, Redis, Elasticsearch) publicly exposed?
* [ ] Are IP whitelists too permissive or misconfigured?

---

### 📷 **30. CSP (Content Security Policy) Misconfiguration**

* [ ] Is CSP missing, too relaxed (`unsafe-inline`, `*`), or not enforced?
* [ ] Can XSS be exploited due to weak/missing CSP?

---



