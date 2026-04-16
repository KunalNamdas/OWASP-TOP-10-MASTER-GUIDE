🛡️: Server-Side Request Forgery (SSRF)
🔹 **Types**
- **Basic SSRF (Response in Response)**
- **Blind SSRF (Out-of-Band/OAST)**
- **Semi-Blind SSRF**
- **XXE to SSRF**
- **SSRF via URL Parsing Inconsistencies**
- **Cloud Metadata Endpoint Abuse**
- **Protocol Smuggling (gopher, dict, file, etc.)**

🔹 **Real-World Scenarios**
- **Cloud Metadata Credential Theft (AWS/GCP/Azure)**
- **Internal Port Scanning & Network Mapping**
- **Bypassing Authentication on Internal Services**
- **Local File Read via file://**
- **Internal Service Exploitation (Redis, Memcached, MySQL)**
- **DDoS Amplification via Internal Services**
- **SSRF as Attack Vector to Achieve RCE**

🔹 **Testing Techniques**
- **Identify All Potential Attack Vectors (URL Parameters, Webhooks, File Uploads, Proxies)**
- **Out-of-Band (OAST) Detection via Collaborator/Interactsh**
- **Protocol and Scheme Fuzzing (http, https, file, dict, gopher, ftp, tftp, ldap, etc.)**
- **Port Scanning via Time-based/Error-based Detection**
- **Cloud Metadata Endpoint Probing (169.254.169.254, 100.100.100.200, etc.)**
- **Bypass Techniques (Alternate IP encoding, DNS Rebinding, Redirects, URL Parsing inconsistencies)**
- **Time-based/Delay Injection for Blind SSRF**

🔹 **Exploitation Vectors**
- **Functionality that Fetches Remote Resources (Avatar uploads, Webhooks, PDF Generation)**
- **File Upload/Processing Endpoints**
- **XML External Entity (XXE) Injection**
- **API Endpoints with URL Parameters (callback, src, dest, redirect, proxy, etc.)**
- **Document Converters and HTML-to-PDF Services**
- **URL Shortener/Preview Services**
- **Server-Side Rendering (SSR) Frameworks**

🔹 **Prevention**
- **Allow List-based Input Validation (Protocol, Domain, IP, Port)**
- **Network Segmentation & "Deny by Default" Firewall Rules**
- **Disable Unused URL Schemas (file://, gopher://, dict://, etc.)**
- **Cloud Metadata Service Hardening (IMDSv2 for AWS, requiring tokens)**
- **Robust URL Parsing & Canonicalization to Prevent Parsing Inconsistencies**
- **Disable HTTP Redirects for Outgoing Requests**
- **Response Handling & Error Message Suppression**

🔹 **Tools**
- **Burp Suite (Professional & Community) with Collaborator**
- **Interactsh (Free OAST Server)**
- **SSRFmap / Gopherus / GopherPHP**
- **ffuf / Dirsearch / GoBuster**
- **Postman**
- **Nikto**
- **Custom Python/Go Scripts**

🔹 **Checklist ✅**
- **Can you access internal services like localhost or 127.0.0.1?**
- **Can you probe the Cloud Metadata Service (169.254.169.254)?**
- **Can you perform internal network scanning?**
- **Can you read local files using file://?**
- **Do you receive Out-of-Band callbacks for Blind SSRF?**
- **Can you bypass weak deny lists or URL encoding restrictions?**
- **Can you escalate SSRF to RCE by exploiting internal services (Redis, etc.)?**

🔐 **Chapter 1: Server-Side Request Forgery – Deep Dive**

🚨 **What is SSRF?**
Server-Side Request Forgery is a web security vulnerability that allows an attacker to induce the server-side application to make HTTP requests to an arbitrary domain of the attacker's choosing. The application functions as a proxy, enabling attackers to target internal systems behind firewalls and network access control lists (ACLs).

🧠 **Why Is It Critical?**
- **OWASP Top 10 #10 (2021)**: A new category added due to rising cloud adoption and complexity.
- **High Impact**: Can lead to full cloud account takeover, internal network compromise, and sensitive data access.
- **Bypasses Security Boundaries**: Since requests originate from the trusted server, they bypass network perimeter controls.

🔄 **Real-World Examples**
| Case | Description |
|------|-------------|
| ✅ Cloud Metadata Theft | Accessing AWS IMDS at `http://169.254.169.254/latest/meta-data/` to steal IAM credentials. |
| ✅ Internal Port Scanning | Mapping internal network topology and identifying open ports on internal hosts. |
| ✅ Local File Read | Using `file:///etc/passwd` to read sensitive local files. |
| ✅ Internal Service Abuse | Exploiting vulnerable internal services like Redis via the gopher protocol. |
| ✅ SSRF to RCE | Chaining SSRF with other vulnerabilities to achieve remote code execution. |

🔍 **Attack Surface Overview**
| Category | Targets |
|----------|---------|
| Functionality | Avatar/image fetching, webhook callbacks, URL previews, document conversion, file import |
| Parameters | `url=`, `dest=`, `redirect=`, `callback=`, `proxy=`, `src=`, `path=`, `file=` |
| Protocols | HTTP, HTTPS, file, gopher, dict, ftp, tftp, ldap |
| Cloud Endpoints | `169.254.169.254` (AWS), `100.100.100.200` (Alibaba), `metadata.google.internal` (GCP), `169.254.169.254` (Azure) |

🧪 **How Do You Test for It?**

**1. 🔁 Identify Attack Vectors**
- Search for functionality that makes server-side requests.
- Analyze API parameters, webhook settings, file upload features, and URL previewers.

**2. 🛠️ Manual Testing Approach**
| Action | What to Do |
|--------|-------------|
| Inject external URL | Replace parameter values with a URL you control (e.g., `http://your-server.com/test`) |
| Test localhost access | Change URL to `http://127.0.0.1:8080/admin` |
| Probe metadata endpoint | Use `http://169.254.169.254/latest/meta-data/` |
| Test file scheme | Use `file:///etc/passwd` |
| Try other protocols | Experiment with `dict://`, `gopher://`, `ftp://` |

**3. 🔧 Blind SSRF Testing**
- Insert a unique domain (e.g., Burp Collaborator or Interactsh payload) into the request.
- Monitor for incoming connections to confirm vulnerability.

**🛡️ Prevention Techniques (For Developers)**
| Strategy | Description |
|----------|-------------|
| ✅ Allow List | Enforce URL schema, port, and destination with a positive allow list. |
| ✅ Deny by Default | Block all internal IP ranges by default; only permit explicitly approved destinations. |
| ✅ Disable Unused Protocols | Disable protocols like `file://`, `gopher://`, `dict://` unless absolutely necessary. |
| ✅ Cloud Hardening | Use IMDSv2 for AWS (requires session token), and disable vulnerable metadata endpoints where possible. |
| ✅ Disable Redirects | Disable automatic HTTP redirection for outgoing requests. |
| ✅ Response Control | Never return raw server responses to clients; suppress detailed error messages. |
| ✅ Auditing & Monitoring | Log all outbound requests and monitor for unusual patterns (e.g., metadata endpoint access). |

📋 **Basic Checklist ✅**
| Check | Status |
|-------|--------|
| Can you access `http://127.0.0.1:80`? | ❌ |
| Can you probe `http://169.254.169.254/latest/meta-data/`? | ❌ |
| Can you read local files using `file://`? | ❌ |
| Can you perform internal port scanning? | ❌ |
| Are outgoing HTTP redirects followed automatically? | ❌ |
| Are other protocols (gopher, dict, etc.) accessible? | ❌ |
| Are error messages suppressed to prevent information leakage? | ✅ |

💡 **Pro Tips**
- Always test for blind SSRF using out-of-band techniques—don't rely solely on direct responses.
- Focus on functionality that makes external requests based on user input (webhooks, file imports, image rendering).
- Check for URL validation bypasses using alternate IP representations, DNS rebinding, and redirect chains.
- Cloud metadata endpoints are the highest-impact targets—always test them in cloud-hosted environments.
- Even if a request doesn't return a response, time-based differences can indicate a vulnerability (semi-blind SSRF).

🧠 **Real Pentester Mindset**
- "What happens if I change this URL to point to an internal service?"
- "Can I make the server request resources from `localhost`?"
- "Does this endpoint send a request to my Collaborator server when I inject a unique domain?"
- "Can I chain this SSRF with other vulnerabilities to escalate privileges?"

🧩 **Chapter 2: Types of Server-Side Request Forgery — In-Depth**

**1️⃣ Basic SSRF (Response in Response)**
- **Description**: The server makes a request to the attacker-controlled URL and returns the full response in the application's response.
- **Impact**: Direct access to internal services, cloud metadata, and local files.
- **Testing**: Look for parameters where the response includes content fetched from the target URL.

**2️⃣ Blind SSRF (Out-of-Band/OAST)**
- **Description**: The server makes a request to the attacker-controlled URL, but no response is returned to the attacker. Detection requires out-of-band monitoring (e.g., Burp Collaborator).
- **Impact**: Still allows internal port scanning, service interaction, and potentially more serious exploitation in combination with other vulnerabilities.
- **Detection**: Inject a unique domain into a parameter and monitor for incoming DNS/HTTP requests.

**3️⃣ Semi-Blind SSRF**
- **Description**: The server makes a request but doesn't return the full response. However, differences in response time, error messages, or HTTP status codes can provide information about the target.
- **Example**: The server may take longer to respond when a port is open versus closed, enabling port scanning.

**4️⃣ XXE to SSRF**
- **Description**: XML External Entity (XXE) injection can be leveraged to perform SSRF attacks. If an application processes XML input and resolves external entities, an attacker can define an entity pointing to an internal resource.
- **Impact**: Access internal services, read local files, and perform port scanning.

**5️⃣ SSRF via URL Parsing Inconsistencies**
- **Description**: Different URL parsing libraries interpret URLs differently. Attackers exploit these inconsistencies to bypass validation checks.
- **Example**: `http://example.com@169.254.169.254/` may bypass a deny list that only checks for `169.254.169.254`.

**6️⃣ Cloud Metadata Endpoint Abuse**
- **Description**: Cloud providers expose a metadata service on internal IP addresses. SSRF can be used to access these endpoints and retrieve sensitive information like IAM credentials, SSH keys, and configuration data.
- **Targets**: AWS (`http://169.254.169.254/latest/meta-data/iam/security-credentials/`), GCP (`http://metadata.google.internal/computeMetadata/v1/`), Azure (`http://169.254.169.254/metadata/instance?api-version=2017-08-01`).

**7️⃣ Protocol Smuggling (gopher, dict, file, etc.)**
- **Description**: SSRF vulnerabilities are not limited to HTTP/HTTPS protocols. Attackers can use other protocols like `gopher://`, `dict://`, `file://`, `ftp://`, and `tftp://` to interact with internal services.
- **Impact**:
    - **`file://`**: Read local files (`file:///etc/passwd`)
    - **`dict://`**: Probe open ports and service banners
    - **`gopher://`**: Interact with TCP-based services (Redis, MySQL, Memcached), potentially achieving RCE

✅ **Summary Table**
| Type | Attack Vector | Detection Method | Impact |
|------|---------------|------------------|--------|
| Basic SSRF | Parameters returning full response | Direct observation of response content | High (direct data access) |
| Blind SSRF | Out-of-band callbacks | Collaborator/Interactsh monitoring | Medium (requires chaining) |
| XXE to SSRF | XML entity injection | XXE payloads with external entities | High (if fully exploitable) |
| Cloud Metadata | Accessing IMDS endpoints | Probing metadata IP addresses | Critical (cloud takeover) |
| Protocol Smuggling | Non-HTTP protocols (gopher, dict) | Fuzzing with different schemes | Critical (RCE possible) |

✅ **Chapter 3: Real-World Scenarios**

**🧩 1. Cloud Metadata Credential Theft**
- **Scenario**: An application hosted on AWS EC2 allows users to specify an image URL for their profile picture. The application fetches the image using the provided URL.
- **Attack**: The attacker provides a URL pointing to the AWS Instance Metadata Service: `http://169.254.169.254/latest/meta-data/iam/security-credentials/`
- **Impact**: The application returns the IAM credentials, allowing the attacker to impersonate the EC2 instance and access cloud resources.

**🔍 2. Internal Port Scanning & Network Mapping**
- **Scenario**: An application has a webhook feature where users can specify a callback URL to receive notifications.
- **Attack**: The attacker iterates through internal IP addresses and ports (e.g., `http://10.0.0.1:8080/admin`, `http://10.0.0.2:22`, etc.).
- **Impact**: The attacker maps the internal network, identifies vulnerable services, and discovers hidden admin panels.

**🗑️ 3. Bypassing Authentication on Internal Services**
- **Scenario**: An internal Redis or Memcached instance is configured without authentication, but is not accessible from the external internet.
- **Attack**: The attacker uses SSRF with the `gopher://` protocol to interact directly with the Redis instance.
- **Impact**: The attacker can read/write to the Redis database, potentially leading to session hijacking or remote code execution (RCE).

**👁️‍🗨️ 4. Local File Read via file://**
- **Scenario**: An application allows users to import data from a URL (e.g., a CSV file) and processes the content.
- **Attack**: The attacker provides a `file://` URI pointing to a sensitive local file, such as `file:///etc/passwd` or `file:///app/config/secrets.properties`.
- **Impact**: Exposure of sensitive system files and application secrets.

**🔐 General Recommendations:**
| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Trusting user-provided URLs without validation | SSRF leading to data breach | Always validate URLs against an allow list |
| Exposing internal services without authentication | Easy lateral movement via SSRF | Implement defense in depth with authentication and network segmentation |
| Allowing non-HTTP protocols (gopher, dict, file) | Escalation to RCE | Disable unnecessary URL schemes |
| Using IMDSv1 (no token required) | Easy credential theft | Upgrade to IMDSv2 with session tokens |

🔍 **Chapter 4: Testing Techniques – SSRF**

**1. 🔧 Parameter Discovery and Fuzzing**
- **What It Is**: Identifying all parameters that might be used to make server-side requests.
- **How**: Use wordlists and fuzzing tools to discover parameters like `url`, `dest`, `callback`, `src`, `path`, `file`, `redirect`, `proxy`, and `webhook`.
- **Tools**: Burp Suite Intruder, ffuf, dirsearch, custom scripts.

**2. 🔐 Out-of-Band (OAST) Detection**
- **What It Is**: Injecting a unique domain (e.g., Burp Collaborator or Interactsh payload) into a request to detect blind SSRF vulnerabilities.
- **How**: Replace a URL parameter value with a unique subdomain (e.g., `http://uniqueid.interactsh.com/test`) and monitor for incoming requests.
- **Tools**: Burp Collaborator, Interactsh.

**3. 📂 Protocol and Scheme Fuzzing**
- **What It Is**: Testing different URL schemes beyond HTTP and HTTPS.
- **Examples**:
    - `file:///etc/passwd`
    - `gopher://localhost:6379/_*1%0d%0a$8%0d%0aFLUSHALL%0d%0a`
    - `dict://localhost:11211/`
    - `ftp://internal-host:21/`
    - `tftp://internal-host:69/README`
- **Tools**: Custom scripts, SSRFmap.

**4. 🔄 Port Scanning via Time/Error-Based Detection**
- **What It Is**: Using SSRF to probe which ports are open on internal hosts by observing differences in response times or error messages.
- **How**: Iterate through a range of ports on `127.0.0.1` or other internal IPs and measure response times or analyze error codes.
- **Tools**: Custom Python scripts, Burp Intruder (time-based payloads).

**5. 🕵️‍♂️ Cloud Metadata Endpoint Probing**
- **What It Is**: Specifically targeting the internal metadata endpoints exposed by cloud providers.
- **Targets**:
    - AWS: `http://169.254.169.254/latest/meta-data/`
    - GCP: `http://metadata.google.internal/computeMetadata/v1/`
    - Azure: `http://169.254.169.254/metadata/instance`
    - Alibaba: `http://100.100.100.200/latest/meta-data/`
- **How**: Inject these URLs into parameters and examine the response for sensitive data.

**6. 🔗 Bypass Techniques (Deny List Evasion)**
- **What It Is**: Many applications implement weak deny lists (e.g., blocking "localhost", "127.0.0.1"). Attackers use various techniques to bypass these restrictions.
- **Techniques**:
    - **Alternate IP representations**: `http://0.0.0.0/`, `http://127.127.127.127/`, `http://2130706433/` (decimal), `http://0x7f000001/` (hex)
    - **DNS Rebinding**: Using a domain that resolves to `127.0.0.1` only after the initial validation.
    - **Redirects**: Using an external URL that redirects to an internal IP address.
    - **URL Parsing Inconsistencies**: `http://safe.com@127.0.0.1/`, `http://127.0.0.1@evil.com/`
    - **Encoding**: URL encoding, double URL encoding, Unicode encoding.

**✅ Quick Tester's Workflow**
1. **Discover parameters**: Use ffuf or Burp to identify all potential URL parameters.
2. **Test for basic SSRF**: Inject a URL you control and observe if the content is reflected.
3. **Test for blind SSRF**: Use Collaborator/Interactsh to detect out-of-band callbacks.
4. **Fuzz protocols**: Test `file://`, `gopher://`, `dict://`, etc.
5. **Probe metadata endpoints**: Target cloud-specific IP addresses.
6. **Perform internal scanning**: Iterate through internal IP ranges and ports.
7. **Attempt bypasses**: Test common deny list evasion techniques.

🧪 **Pro Tip**: Combine techniques – use out-of-band detection on all identified URL parameters, then use successful callbacks as confirmation for further exploitation (e.g., port scanning).

✅ **Chapter 5: Exploitation Vectors – SSRF**

**🔹 1. Functionality that Fetches Remote Resources**
- **Description**: Any feature that makes HTTP requests to user-supplied URLs is a potential SSRF vector.
- **Examples**:
    - Avatar/image URL fetching
    - Webhook/callback configuration
    - URL preview/link unfurling
    - RSS feed import
    - PDF generation from HTML
    - Website screenshot services

**🔹 2. File Upload/Processing Endpoints**
- **Description**: Features that process uploaded files may also support URLs as input, leading to SSRF.
- **Examples**:
    - "Import from URL" functionality
    - File conversion services
    - Document processing (e.g., converting a document from a URL)
- **Real Case**: CVE-2026-24767 – Blind SSRF via unvalidated HEAD request in uploadViaURL functionality.

**🔹 3. XML External Entity (XXE) Injection**
- **Description**: If an application processes XML input and resolves external entities, attackers can define an external entity pointing to an internal resource.
- **Example**:
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/" > ]>
    <stockCheck><productId>&xxe;</productId></stockCheck>
    ```
- **Impact**: The server resolves the external entity and can leak the response.

**🔹 4. API Endpoints with URL Parameters**
- **Description**: APIs often include parameters for specifying callback URLs, redirect URIs, or external endpoints.
- **Common Parameter Names**: `callback`, `dest`, `redirect`, `returnTo`, `next`, `out`, `view`, `dir`, `show`, `file`, `document`, `folder`, `root`, `path`, `src`, `url`, `uri`, `location`, `reference`, `site`, `host`

**🔹 5. Document Converters and HTML-to-PDF Services**
- **Description**: Services that convert HTML documents to PDF often make requests to embedded resources (images, CSS, JavaScript).
- **Attack**: If the service fetches resources from user-provided URLs, an attacker can inject internal URLs.
- **Impact**: The resulting PDF may contain the response from internal services, leaking sensitive data.

**🔹 6. URL Shortener/Preview Services**
- **Description**: URL shorteners and link preview services need to fetch the target URL to generate a preview.
- **Attack**: The attacker provides a URL pointing to an internal service.
- **Impact**: The service makes the request, potentially exposing information through the preview or logs.

**🔹 7. Server-Side Rendering (SSR) Frameworks**
- **Description**: Some frontend frameworks perform server-side rendering that may include making requests to external APIs.
- **Attack**: If the framework allows user input to influence these requests, SSRF is possible.

**📌 Exploitation-Oriented Checklist**
| Checkpoint | ✅ |
|------------|----|
| Can you make the server request an external URL you control? | ⬜ |
| Can you access `http://127.0.0.1:80` or `localhost`? | ⬜ |
| Can you read local files using `file://`? | ⬜ |
| Can you access the cloud metadata endpoint? | ⬜ |
| Can you perform internal port scanning? | ⬜ |
| Can you interact with non-HTTP services (gopher, dict)? | ⬜ |
| Can you bypass weak deny lists using encoding or DNS rebinding? | ⬜ |

✅ **Chapter 6: Prevention of SSRF**

**🔒 1. Allow List-Based Input Validation**
- **What It Is**: Only permit requests to explicitly approved domains, IP addresses, and URL patterns. Reject all others by default.
- **Implementation**:
    - Validate protocol: Only allow `http` and `https` (if needed)
    - Validate domain: Check against an allow list of trusted domains
    - Validate IP: Reject private and reserved IP ranges (RFC 1918, loopback, link-local, etc.)
    - Validate port: Only allow standard ports (80, 443, etc.)
- **Important**: Deny lists are easily bypassed. Always use an allow list approach.

**🔑 2. Network Segmentation & "Deny by Default" Firewall Rules**
- **What It Is**: Segment remote resource access functionality into separate networks to reduce the impact of SSRF. Enforce "deny by default" firewall policies to block all but essential intranet traffic.
- **Implementation**:
    - Place external-facing services in a DMZ with restricted egress
    - Implement egress filtering to block requests to internal IP ranges
    - Use network access control lists (ACLs) to limit outbound traffic

**🛡️ 3. Disable Unused URL Schemas**
- **What It Is**: Disable protocols like `file://`, `gopher://`, `dict://`, `ftp://`, and `tftp://` unless absolutely necessary.
- **Why**: These protocols significantly increase the impact of SSRF, enabling local file reads and interaction with internal TCP services.

**🔐 4. Cloud Metadata Service Hardening**
- **What It Is**: Use the most secure version of the instance metadata service (IMDSv2 for AWS) which requires a session token for access.
- **Implementation**:
    - Upgrade to IMDSv2 on all EC2 instances
    - Disable IMDSv1 if possible
    - Use network policies to block access to metadata endpoints at the firewall level
    - Monitor for unusual access patterns to metadata endpoints

**🔀 5. Robust URL Parsing & Canonicalization**
- **What It Is**: Parse and canonicalize URLs before validation to prevent inconsistencies in URL interpretation.
- **Why**: Different URL parsing libraries interpret URLs differently, leading to bypasses (e.g., `http://safe.com@127.0.0.1/`).
- **Implementation**:
    - Use a well-tested URL parsing library
    - Canonicalize the URL (resolve relative paths, normalize encoding)
    - Validate each component (scheme, host, port, path) separately

**⏱️ 6. Disable HTTP Redirects**
- **What It Is**: Disable automatic redirection for outgoing requests.
- **Why**: Attackers can use an external URL that redirects to an internal IP address, bypassing initial validation.
- **Implementation**:
    - Configure the HTTP client to not follow redirects
    - Validate the final destination after redirects, if redirects are necessary

**🧾 7. Response Handling & Error Message Suppression**
- **What It Is**: Never return raw server responses to clients. Suppress detailed error messages that could reveal information about internal services.
- **Why**: Detailed error messages can help attackers map internal networks and services.
- **Implementation**:
    - Return generic error messages for all failures
    - Log detailed errors server-side for debugging

**📋 Summary Checklist for Prevention ✅**
| Area | Check |
|------|-------|
| Allow list implementation | ✅ Only permitted domains/protocols allowed |
| Deny by default network policies | ✅ Egress filtering blocks internal IP ranges |
| Unused protocols disabled | ✅ file://, gopher://, dict:// disabled |
| Cloud metadata hardening | ✅ IMDSv2 enabled, metadata access monitored |
| Robust URL parsing | ✅ Canonicalization before validation |
| Redirects disabled | ✅ HTTP client configured to not follow redirects |
| Error message suppression | ✅ Generic errors returned to clients |

🔧 **Chapter 7: Tools – Deep Dive for SSRF**

**🔹 1. Burp Suite (Professional & Community)**
- **Purpose**: Intercept, modify, and replay requests to test for SSRF.
- **Key Features**:
    - **Repeater**: Manually modify URL parameters and test different payloads
    - **Intruder**: Automate fuzzing of URL parameters, ports, and internal IPs
    - **Collaborator**: Out-of-band detection for blind SSRF (Professional only)
    - **Extensions**: SSRF plugins, custom payload generators

**🔹 2. Interactsh (Free OAST Server)**
- **Purpose**: Open-source alternative to Burp Collaborator for detecting blind SSRF.
- **Features**:
    - Generates unique subdomains for injection
    - Monitors for DNS and HTTP interactions
    - Free and easy to use
    - Integrates with Burp via plugins

**🔹 3. SSRFmap / Gopherus / GopherPHP**
- **Purpose**: Automated SSRF exploitation tools with support for protocol smuggling.
- **Features**:
    - **SSRFmap**: Automated SSRF fuzzer and exploitation framework
    - **Gopherus**: Generates gopher protocol payloads for exploiting Redis, MySQL, etc.
    - **GopherPHP**: Gopher protocol payload generator for SSRF attacks

**🔹 4. ffuf / Dirsearch / GoBuster**
- **Purpose**: Fuzzing and discovery of hidden endpoints and parameters.
- **Use Case**: Discover parameters that may be vulnerable to SSRF (e.g., `url=`, `dest=`, `callback=`).

**🔹 5. OWASP ZAP**
- **Purpose**: Open-source web application scanner.
- **Features**:
    - Automated scanning for SSRF vulnerabilities
    - Manual request interception and modification
    - Fuzzing capabilities

**🔹 6. Postman**
- **Purpose**: Manual API testing.
- **Use Case**: Send crafted requests to API endpoints with modified URL parameters.

**🔹 7. Nikto**
- **Purpose**: Web server scanner.
- **Use Case**: Basic SSRF detection and vulnerability scanning.

**🧪 Pro Tip**:
- **Combine tools**: Use ffuf for parameter discovery, Burp Collaborator/Interactsh for detection, and SSRFmap/Gopherus for exploitation.
- **Custom scripts**: Write Python scripts for automated port scanning and internal network enumeration via SSRF.

✅ **Chapter 8: SSRF Checklist (Deep Dive)**

**🔍 1. Can you access internal services like localhost or 127.0.0.1?**
- **Test**: Replace URL parameters with `http://127.0.0.1:80`, `http://localhost:8080`, `http://0.0.0.0:22`, etc.
- **Why**: Successful access indicates the server can reach internal services.
- **Tools**: Burp Repeater, curl.

**🛠 2. Can you probe the Cloud Metadata Service?**
- **Test**: Inject cloud metadata endpoints:
    - AWS: `http://169.254.169.254/latest/meta-data/`
    - GCP: `http://metadata.google.internal/computeMetadata/v1/`
    - Azure: `http://169.254.169.254/metadata/instance`
- **Why**: Metadata often contains IAM credentials, SSH keys, and configuration data.
- **Impact**: Critical – full cloud account takeover possible.

**🌀 3. Can you perform internal network scanning?**
- **Test**: Iterate through internal IP ranges (RFC 1918) and common ports:
    - `http://10.0.0.1:8080/admin`
    - `http://172.16.0.1:22`
    - `http://192.168.1.1:3306`
- **Why**: Mapping the internal network reveals attack surface for lateral movement.
- **Tools**: Burp Intruder, custom scripts.

**📁 4. Can you read local files using file://?**
- **Test**: Use `file://` protocol to access sensitive local files:
    - `file:///etc/passwd`
    - `file:///proc/self/environ`
    - `file:///app/config/secrets.properties`
- **Why**: Exposure of system files and application secrets.
- **Note**: Only works if the `file://` protocol is enabled.

**🔌 5. Do you receive Out-of-Band callbacks for Blind SSRF?**
- **Test**: Inject a unique domain (e.g., `http://uniqueid.interactsh.com/test`) into URL parameters and monitor for DNS/HTTP interactions.
- **Why**: Blind SSRF is still exploitable and can be chained with other vulnerabilities.
- **Tools**: Burp Collaborator, Interactsh.

**🧬 6. Can you bypass weak deny lists or URL encoding restrictions?**
- **Test**: Try common bypass techniques:
    - Alternate IP representations: `http://0.0.0.0/`, `http://2130706433/`
    - URL encoding: `http://127.0.0.1%2Fadmin`
    - DNS rebinding
    - Redirect chains
- **Why**: Many applications implement easily bypassed deny lists.

**⚡ 7. Can you escalate SSRF to RCE by exploiting internal services?**
- **Test**: If internal services like Redis, Memcached, or MySQL are reachable via `gopher://`:
    - Use `gopher://` to send raw TCP commands to Redis
    - Write a SSH key or cron job to achieve RCE
- **Why**: SSRF combined with vulnerable internal services can lead to complete system compromise.
- **Tools**: Gopherus, custom payload generators.

**📋 8. Test for SSRF via XML External Entity (XXE)**
- **Test**: If the application processes XML input, try injecting an external entity pointing to an internal URL:
    ```xml
    <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/" > ]>
    ```
- **Why**: XXE can be leveraged to perform SSRF attacks.

**🔧 Pro Tip**: Always test with multiple roles (unauthenticated, authenticated low-privilege, and admin) – SSRF vulnerabilities can exist in any user context. Look for SSRF in mobile apps and thick clients as well.

🧠 **SSRF – Advanced Mind Map (Text Format)**

**🧩 1. Introduction to SSRF**
- 🔐 Definition: Inducing server to make arbitrary requests
- 🧨 OWASP Rank: #10 in OWASP Top 10 (new category)
- 🔍 Impact: Internal network compromise, cloud takeover, data breach

**🧪 2. Types of SSRF**
- 📡 **Basic SSRF (Response in Response)**
    - Direct access to internal resources
    - Easy to detect and exploit
- 🕶️ **Blind SSRF (Out-of-Band/OAST)**
    - No response returned to attacker
    - Requires external monitoring (Collaborator/Interactsh)
- 🔀 **Semi-Blind SSRF**
    - Time/error-based detection
    - Enables port scanning
- 📄 **XXE to SSRF**
    - XML external entity injection
    - Leveraged for SSRF attacks
- ☁️ **Cloud Metadata Endpoint Abuse**
    - AWS/GCP/Azure metadata services
    - High-impact credential theft
- 🔌 **Protocol Smuggling (gopher, dict, file, etc.)**
    - Non-HTTP protocol access
    - Escalation to RCE possible

**🎯 3. Real-World Scenarios**
- ☁️ **Cloud Metadata Theft** – AWS IMDS credential extraction
- 🔍 **Internal Port Scanning** – Network mapping via SSRF
- 🔓 **Bypassing Authentication** – Accessing internal services without auth
- 📁 **Local File Read** – file:// protocol access to system files
- 🔌 **Internal Service Exploitation** – Redis, Memcached, MySQL abuse
- 💥 **SSRF to RCE** – Chaining with other vulnerabilities

**🧪 4. Testing Techniques**
- 🔧 **Parameter Discovery & Fuzzing** – Identify potential SSRF vectors
- 🔐 **Out-of-Band (OAST) Detection** – Blind SSRF detection
- 📂 **Protocol and Scheme Fuzzing** – HTTP, HTTPS, file, gopher, dict, etc.
- 🔄 **Port Scanning via Time/Error-Based Detection** – Internal network mapping
- 🕵️‍♂️ **Cloud Metadata Endpoint Probing** – Target metadata IP addresses
- 🔗 **Bypass Techniques** – Deny list evasion (encoding, DNS rebinding, redirects)

**🚨 5. Exploitation Vectors**
- 📡 **Functionality that Fetches Remote Resources** – Avatars, webhooks, URL previews
- 📁 **File Upload/Processing Endpoints** – Import from URL features
- 📄 **XML External Entity (XXE) Injection** – XXE to SSRF
- 🔌 **API Endpoints with URL Parameters** – callback, dest, redirect, proxy
- 📑 **Document Converters and HTML-to-PDF Services** – Resource fetching
- 🔗 **URL Shortener/Preview Services** – Link unfurling
- ⚛️ **Server-Side Rendering (SSR) Frameworks** – SSR-based requests

**🛡️ 6. Prevention**
- ✅ **Allow List-Based Input Validation** – Protocol, domain, IP, port allow listing
- 🔑 **Network Segmentation & "Deny by Default"** – Egress filtering, DMZ
- 🛡️ **Disable Unused URL Schemas** – file://, gopher://, dict:// disabled
- 🔐 **Cloud Metadata Service Hardening** – IMDSv2, token requirement
- 🔀 **Robust URL Parsing & Canonicalization** – Prevent parsing inconsistencies
- ⏱️ **Disable HTTP Redirects** – Prevent redirect-based bypasses
- 🧾 **Response Handling & Error Message Suppression** – Generic error messages

**🔧 7. Tools**
- 🔍 **Burp Suite** – Repeater, Intruder, Collaborator
- 🔐 **Interactsh** – Free OAST server
- ⚙️ **SSRFmap / Gopherus / GopherPHP** – Automated exploitation
- 🚀 **ffuf / Dirsearch / GoBuster** – Fuzzing and discovery
- 🧙‍♂️ **OWASP ZAP** – Open-source scanning
- 📡 **Postman** – Manual API testing

**✅ 8. Checklist**
- ✅ **Access internal services (localhost, 127.0.0.1)**
- ✅ **Probe cloud metadata endpoint**
- ✅ **Perform internal network scanning**
- ✅ **Read local files via file://**
- ✅ **Receive Out-of-Band callbacks for Blind SSRF**
- ✅ **Bypass weak deny lists or URL encoding restrictions**
- ✅ **Escalate SSRF to RCE by exploiting internal services**
- ✅ **Test for SSRF via XXE**
