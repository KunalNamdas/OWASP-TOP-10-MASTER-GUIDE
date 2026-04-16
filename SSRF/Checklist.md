## ✅ **Insecure Design Checklist for SSRF (Offensive Security Focused)**

### 🔍 1. **Lack of URL Allow-List Validation**

* [ ] Does the application accept user-supplied URLs without validating against an **allow list** of permitted domains or IPs?
* [ ] Are only **deny lists** used (e.g., blocking `localhost`, `127.0.0.1`), which are easily bypassed?
* [ ] Is there **no protocol validation** (HTTP/HTTPS only)?

> 🛠 Try injecting `http://169.254.169.254/`, `file:///etc/passwd`, or `gopher://localhost:6379/` to bypass weak checks.

---

### 🔐 2. **Trusting User Input in Server-Side Requests**

* [ ] Are **external URL parameters** (e.g., `url=`, `dest=`, `callback=`, `webhook=`) trusted without sanitization?
* [ ] Does the application fetch remote resources (avatars, PDFs, screenshots) based on user input?
* [ ] Is there **no separation** between user-controlled URLs and internal service endpoints?

> 🛠 Fuzz all parameters that accept URLs or file paths using Burp Intruder or ffuf.

---

### 📡 3. **Cloud Metadata Service Exposure by Design**

* [ ] Is the cloud metadata endpoint (e.g., `169.254.169.254` for AWS) **not blocked** at the network or application level?
* [ ] Does the design rely on **IMDSv1** (no token required) instead of IMDSv2?
* [ ] Are there **no network policies** restricting access to metadata IPs from application servers?

> 🛠 Probe `http://169.254.169.254/latest/meta-data/iam/security-credentials/` to steal IAM credentials.

---

### 🌐 4. **Internal Network Discovery Enabled by Design**

* [ ] Does the application allow requests to **RFC 1918 IP ranges** (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)?
* [ ] Are **localhost** and **loopback** addresses accessible (`127.0.0.1`, `0.0.0.0`, `localhost`)?
* [ ] Is there **no egress filtering** on outbound HTTP requests from the server?

> 🛠 Perform internal port scanning by iterating through IPs and ports (e.g., `http://10.0.0.1:22`, `http://192.168.1.1:8080`).

---

### 📁 5. **Support for Dangerous URL Schemes**

* [ ] Does the application support `file://`, `gopher://`, `dict://`, `ftp://`, or `tftp://` protocols?
* [ ] Are these schemes **intentionally enabled** for “flexibility” without security review?
* [ ] Is there **no protocol allow list** restricting to `http`/`https` only?

> 🛠 Use `file:///etc/passwd` to read local files, or `gopher://` to interact with internal Redis/Memcached for RCE.

---

### 🔄 6. **Automatic HTTP Redirect Following**

* [ ] Does the server **follow HTTP redirects** (3xx) automatically without validation?
* [ ] Can an external URL redirect to an internal IP or localhost?
* [ ] Is there **no limit** on redirect depth or destination checks?

> 🛠 Set up a redirect chain: `http://attacker.com/redirect` → `http://127.0.0.1/admin` to bypass initial validation.

---

### 🧩 7. **URL Parsing Inconsistencies in Design**

* [ ] Does the application use **custom or inconsistent URL parsers** across different modules?
* [ ] Are URLs **not canonicalized** before validation (e.g., handling of `@`, `#`, `%`)?
* [ ] Does the design ignore **different encoding schemes** (double URL encode, Unicode)?

> 🛠 Bypass filters with `http://safe.com@127.0.0.1/`, `http://0x7f000001/`, or `http://localhost%2f@evil.com/`.

---

### 🧠 8. **No Threat Modeling for Outbound Requests**

* [ ] Are **outbound request features** (webhooks, URL previews, file imports) missing from threat models?
* [ ] Is there **no analysis** of what internal services could be reached via SSRF?
* [ ] Are there **no abuse cases** documented (e.g., metadata access, port scanning)?

> 🛠 Map the internal attack surface by testing common internal service ports (Redis 6379, MySQL 3306, etc.).

---

### 🔁 9. **Blind SSRF Not Considered in Design**

* [ ] Does the design assume SSRF is only exploitable when the response is returned?
* [ ] Are there **no out-of-band detection mechanisms** (e.g., monitoring DNS/HTTP callbacks)?
* [ ] Is error handling **verbose**, leaking whether a request succeeded or failed?

> 🛠 Inject a unique subdomain (e.g., `http://your-collaborator.com`) and monitor for DNS/HTTP interactions.

---

### 🚪 10. **No Segmentation for Internal Services**

* [ ] Are internal services (Redis, Memcached, databases) accessible from the same server that handles user URLs?
* [ ] Is there **no network segmentation** between the web tier and internal backends?
* [ ] Are internal services **unauthenticated** by design (e.g., default Redis config)?

> 🛠 Use SSRF to issue `FLUSHALL` to Redis or dump database contents via `gopher://`.

---

### 📦 11. **Insecure Design of File Upload/Processing Features**

* [ ] Does the application accept URLs for **file processing** (e.g., “Import from URL”) without sandboxing?
* [ ] Are **image/video fetching** endpoints vulnerable to SSRF?
* [ ] Is there **no timeout or size limit** on fetched resources?

> 🛠 Supply `http://169.254.169.254/latest/user-data/` to leak cloud init scripts.

---

### 🔌 12. **Missing Request Timeout & Resource Limits**

* [ ] Does the design lack **timeouts** for outbound requests, allowing slowloris-style attacks?
* [ ] Are there **no limits** on response size, leading to memory exhaustion?
* [ ] Can you cause **denial of service** by making the server fetch huge files or slow endpoints?

> 🛠 Use `http://your-slow-server.com/delay/30` to tie up worker threads.

---

### 🧱 13. **No Isolation for PDF/HTML Generation Services**

* [ ] Are **document converters** (HTML to PDF) that fetch remote resources designed without SSRF protection?
* [ ] Does the converter have access to internal network or local files?
* [ ] Are **external resources** (CSS, images, fonts) loaded from user-supplied URLs?

> 🛠 Generate a PDF that includes `http://127.0.0.1:8080/admin` – the PDF may contain the internal response.

---

### 🗺️ 14. **Admin/Debug Endpoints Exposed Internally Only**

* [ ] Does the design assume that **internal endpoints** are safe because they’re not publicly routable?
* [ ] Are sensitive panels (`/admin`, `/metrics`, `/debug/pprof`) accessible via SSRF?
* [ ] Is there **no additional authentication** for internal-only services?

> 🛠 Use SSRF to access `http://localhost:8080/actuator/env` to leak environment variables.

---

### 🔓 15. **Lack of Response Validation & Filtering**

* [ ] Does the application **return raw responses** from fetched URLs back to the user?
* [ ] Are there **no checks** on response content (e.g., stripping sensitive headers)?
* [ ] Is error message **detailed** (e.g., “Connection refused”, “No route to host”) revealing network topology?

> 🛠 Use error differences to map open ports and internal services.

---

### 🧪 16. **No Rate Limiting on SSRF Primitives**

* [ ] Can an attacker **spam SSRF requests** to perform large-scale port scanning or DoS?
* [ ] Is there **no rate limiting** per user or IP for URL-fetching endpoints?
* [ ] Are there **no alerts** for anomalous outbound request patterns?

> 🛠 Use Burp Intruder with a list of 10,000 internal IPs to map the network.

---

### 🔁 17. **Session or Token Replay Allowed for SSRF**

* [ ] Can SSRF be used to **replay authenticated requests** to internal APIs?
* [ ] Does the server forward **session cookies** or authorization headers when making outbound requests?
* [ ] Is there **no isolation** between user contexts and internal request contexts?

> 🛠 If the server forwards your session token, use SSRF to call internal APIs as yourself.

---

### 📄 18. **Insecure Design of XML/XXE Processing**

* [ ] Does the application process **XML documents** with external entity resolution enabled?
* [ ] Is there **no disabling** of `http://`, `file://`, `ftp://` in XML parsers?
* [ ] Are XXE attacks **not considered** in design reviews?

> 🛠 Inject `<!ENTITY % xxe SYSTEM "http://169.254.169.254/">` to exfiltrate cloud metadata.

---

### 🔗 19. **Unsafe Use of `libcurl` or Similar Libraries**

* [ ] Does the design rely on `libcurl` or other libraries with **dangerous options enabled** (e.g., `CURLOPT_FOLLOWLOCATION`, `CURLOPT_PROTOCOLS` all)?
* [ ] Are there **no restrictions** on protocols or redirects at the library level?
* [ ] Is the library version **outdated** with known SSRF bypasses?

> 🛠 Test for CRLF injection or protocol smuggling via `libcurl` quirks.

---

### 🧩 20. **No Hardening for Server-Side Rendering (SSR)**

* [ ] Does the SSR framework **fetch user-supplied URLs** for data or components?
* [ ] Are there **no sandboxing** or network restrictions for SSR processes?
* [ ] Can SSR be tricked into making internal requests?

> 🛠 Try to include `http://localhost:3000/api/internal` in SSR data fetching.

---

### 🧠 21. **Lack of Design for DNS Rebinding Protection**

* [ ] Does the application **resolve DNS once** and trust the IP indefinitely?
* [ ] Is there **no second DNS lookup** after validation?
* [ ] Are **short TTL values** not handled securely?

> 🛠 Use a domain that resolves to a safe IP first, then switches to `127.0.0.1` (DNS rebinding attack).

---

### 🎯 22. **Misuse of `X-Forwarded-For` and Similar Headers**

* [ ] Does the application trust `X-Forwarded-For`, `X-Original-URL`, or `X-Rewrite-URL` headers to make backend requests?
* [ ] Are these headers **not sanitized** or validated?
* [ ] Can an attacker control the target host via headers?

> 🛠 Set `X-Original-URL: http://169.254.169.254/` to bypass frontend restrictions.

---

### 🔐 23. **Insecure Design of Webhook Callbacks**

* [ ] Are webhook URLs **user-configurable** without any domain/IP validation?
* [ ] Does the system send **sensitive data** (API keys, tokens) to webhook endpoints?
* [ ] Is there **no retry limit** or authentication for webhook deliveries?

> 🛠 Set webhook to `http://internal-service/admin/delete` to trigger destructive actions.

---

### 🧪 24. **No Design for Out-of-Band (OAST) Monitoring**

* [ ] Does the design assume that **lack of response** means no SSRF risk?
* [ ] Are there **no mechanisms** to detect blind SSRF (e.g., DNS monitoring, network logs)?
* [ ] Is there **no integration** with security monitoring for unusual outbound connections?

> 🛠 Use Interactsh or Burp Collaborator to confirm blind SSRF even when no data is returned.

---

### 🛡️ 25. **Trusting Local Hostnames / DNS Search Domains**

* [ ] Does the application resolve **internal hostnames** (e.g., `redis.internal`, `db.prod`) without validation?
* [ ] Are there **no checks** to prevent requests to these domains?
* [ ] Is the DNS search domain **leaked** or guessable?

> 🛠 Try `http://redis.internal:6379/` or `http://db.prod:3306/` to access internal services.

---

### 📦 26. **Insecure Deserialization Leading to SSRF**

* [ ] Does the design include **deserialization of user-supplied objects** that can trigger network requests?
* [ ] Are Java `URL` objects or .NET `WebClient` instances created from untrusted data?
* [ ] Is there **no allow list** for classes or protocols during deserialization?

> 🛠 Craft a serialized payload that instantiates a `URL` object pointing to internal metadata.

---

### 🔁 27. **No Design for Request Deduplication or Cache Poisoning**

* [ ] Does the application **cache responses** from user-supplied URLs?
* [ ] Can an attacker poison the cache with internal responses (e.g., metadata)?
* [ ] Are there **no cache keys** that include the full URL and authentication context?

> 🛠 Fetch `http://169.254.169.254/latest/meta-data/` once, and see if it’s cached for other users.

---

### 🧩 28. **Improper Handling of Non-HTTP Services via SSRF**

* [ ] Does the design allow interaction with **non-web services** (e.g., SMTP, POP3, IMAP) via URL schemes?
* [ ] Are there **no checks** to prevent scanning or exploitation of these services?
* [ ] Can SSRF be used to **send emails** or interact with internal mail servers?

> 🛠 Use `smtp://internal-mail:25/` to fingerprint or exploit mail services.

---

### 📍 29. **No Isolation for Serverless / Lambda Functions**

* [ ] In serverless designs, does the function have **unrestricted outbound network access**?
* [ ] Are there **no VPC settings** restricting internal IP access?
* [ ] Can SSRF from a Lambda function access metadata or internal APIs?

> 🛠 Test for SSRF in serverless functions – they often have access to internal AWS services.

---

### 🧠 30. **Missing Design Review for Third-Party Integrations**

* [ ] Are **third-party APIs** that fetch URLs (e.g., image CDNs, webhook relays) trusted without SSRF testing?
* [ ] Does the design assume third-party services are **immune to SSRF**?
* [ ] Are there **no SLAs or security requirements** for vendors regarding outbound requests?

> 🛠 If your app sends a URL to a third-party service, test if that service can be used as an SSRF vector back into your internal network.

---
