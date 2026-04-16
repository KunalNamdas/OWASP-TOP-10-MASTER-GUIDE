### ✅ **Vulnerable & Outdated Components Checklist (Offensive Security Focused)**

---

#### 🧱 **1. Outdated Libraries & Dependencies**

* [ ] Are outdated versions of frontend libraries (e.g., jQuery, AngularJS, React) in use?
* [ ] Are old server-side dependencies used (e.g., Spring, Express, Django, Rails)?
* [ ] Is software using EOL (End-of-Life) packages without security patches?
* [ ] Are known vulnerable libraries flagged in Snyk, Trivy, or OSS Index?

---

#### 🔍 **2. Unpatched Server Software & Services**

* [ ] Is the web server running outdated Apache, Nginx, or IIS versions?
* [ ] Are known exploits available for the current OS or kernel version?
* [ ] Is outdated OpenSSL, SSH, or PHP in use?
* [ ] Are CVEs publicly available for versions identified via banner grabbing?

---

#### 🧬 **3. Unmaintained or Deprecated Components**

* [ ] Is any open-source component unmaintained or abandoned by developers?
* [ ] Is legacy software used in critical parts (e.g., Flash, Java Applets)?
* [ ] Are CMS platforms outdated (e.g., WordPress, Joomla, Drupal)?
* [ ] Are deprecated frameworks still active (e.g., AngularJS after EOL)?

---

#### 🗝️ **4. Vulnerable Plugin, Module, or Extension**

* [ ] Are WordPress/Joomla plugins outdated or unverified?
* [ ] Are browser extensions interacting with the application insecurely?
* [ ] Are third-party npm/pip/rubygems modules outdated or known to be malicious?
* [ ] Are plugins exposing known XSS/RCE/LFI vectors?

---

#### 📦 **5. Container & Image Vulnerabilities**

* [ ] Are container base images (e.g., Alpine, Ubuntu, Debian) outdated?
* [ ] Are vulnerable packages baked into the container images?
* [ ] Are containers built with old dependencies (not using latest tags or pinned versions)?
* [ ] Is no vulnerability scanning tool used (e.g., Trivy, Grype, Dockle)?

---

#### 💾 **6. Insecure or Old Database Versions**

* [ ] Is MySQL, PostgreSQL, or MongoDB running outdated versions?
* [ ] Are default credentials or configurations still enabled from older versions?
* [ ] Are known CVEs affecting current database version?
* [ ] Is database interface accessible publicly due to outdated network architecture?

---

#### 🧪 **7. Technology Stack Fingerprinting**

* [ ] Can you identify vulnerable tech via tools like `whatweb`, `wappalyzer`, `nmap`?
* [ ] Are there signs of outdated tech via error pages, JS libraries, headers?
* [ ] Are build/version files exposed (`/composer.lock`, `/package-lock.json`, `.env`)?

---

#### 📂 **8. Public Exploits Available for Components**

* [ ] Are any components found listed in Exploit-DB, GitHub, or Metasploit?
* [ ] Are security advisories ignored or postponed for legacy apps?
* [ ] Are older CMS themes/templates containing known issues?

---

#### 🔄 **9. Lack of Dependency Management or Update Strategy**

* [ ] Are dependencies not locked (e.g., no `package-lock.json`, `requirements.txt`)?
* [ ] Is there no automated system for tracking CVEs or updates?
* [ ] Is no SBOM (Software Bill of Materials) generated or maintained?
* [ ] Are developers manually copying open-source code from forums or blogs?

---

#### 🚧 **10. CDN / External Dependency Risks**

* [ ] Are external JS/CSS dependencies loaded from unknown or outdated CDNs?
* [ ] Are files pulled from compromised third-party repositories or mirrors?
* [ ] Are fallback libraries local versions and outdated?

---

### 🔍 Bonus: Enumeration Tips & Tools

| Vector              | Tools / Techniques                                         |
| ------------------- | ---------------------------------------------------------- |
| Identify Tech Stack | `whatweb`, `nmap`, `httpx`, `wappalyzer`                   |
| CVE Search          | `searchsploit`, `vulners`, `nuclei`, `Trivy`               |
| Dependency Audit    | `npm audit`, `yarn audit`, `safety`, `bandit`, `pip-audit` |
| Container Scanning  | `trivy`, `grype`, `dockle`, `clair`                        |
| SBOM Generation     | `cyclonedx`, `syft`, `tern`                                |

---

### ✅ **Vulnerable & Outdated Components – Extended Checklist (Offensive Security Focused)**

---

#### 🛠️ **11. Vulnerable Build Tools or Compilers**

* [ ] Are applications built with outdated versions of compilers (e.g., GCC, Clang)?
* [ ] Is the build toolchain (e.g., Gradle, Maven, Webpack) using vulnerable versions?
* [ ] Is there a lack of reproducible builds or checksum validation?

---

#### 🔧 **12. Outdated Admin Panels or Management Consoles**

* [ ] Are admin panels (e.g., phpMyAdmin, Kibana, Jenkins) outdated or publicly accessible?
* [ ] Are they using versions with known RCE, XSS, or SSRF vulnerabilities?

---

#### 🎮 **13. Known CVEs in JavaScript/Frontend Packages**

* [ ] Are packages like Lodash, Moment.js, jQuery UI outdated with known CVEs?
* [ ] Are frontend packages in `node_modules` not being updated automatically?
* [ ] Is `npm audit` or `yarn audit` showing critical/high vulnerabilities ignored?

---

#### 📜 **14. Old or Vulnerable License Files**

* [ ] Are libraries included that violate licensing or allow backdoors (e.g., malware-laced forks)?
* [ ] Are GPL or unverified codebases bundled with proprietary systems?

---

#### 🧾 **15. Insecure Analytics or Third-Party Trackers**

* [ ] Are old analytics scripts (e.g., Google Analytics, Hotjar) used with XSS-prone versions?
* [ ] Are trackers calling unverified or outdated external endpoints?

---

#### 🕳️ **16. Embedded Devices or IoT Firmware**

* [ ] Are embedded devices (routers, IP cameras, etc.) using outdated firmware?
* [ ] Are known exploits (e.g., Mirai) still applicable to discovered firmware versions?

---

#### 📱 **17. Mobile SDKs and Dependencies**

* [ ] Are Android/iOS apps using outdated third-party SDKs (e.g., Facebook, Firebase)?
* [ ] Is the app built on an outdated Android API or iOS SDK version?
* [ ] Are binary libraries (.so/.a files) embedded and outdated?

---

#### 🧰 **18. CI/CD Pipelines with Vulnerable Images or Actions**

* [ ] Is the pipeline using base images (e.g., `python:2.7`) that are EOL?
* [ ] Are GitHub Actions or GitLab templates pulling outdated scripts/tools?
* [ ] Are third-party CI steps vulnerable to RCE or injection attacks?

---

#### 🔁 **19. Vulnerable or Abandoned API Endpoints**

* [ ] Are there legacy API versions (`/api/v1/`) still active?
* [ ] Is there evidence of insecure deprecated REST or SOAP endpoints still exposed?

---

#### 🔄 **20. Stale OAuth / SSO Integrations**

* [ ] Are old OAuth flows used (e.g., Implicit Grant)?
* [ ] Is SAML 1.0 or older SSO mechanisms still in use with known bypasses?

---

#### 🧱 **21. Known Vulns in CMS Themes & Templates**

* [ ] Are WordPress/Joomla themes using JS/CSS/plugins with known vulnerabilities?
* [ ] Are outdated builder plugins (e.g., WPBakery, Elementor) active?

---

#### 🧬 **22. Language-Level Vulnerabilities**

* [ ] Are apps running vulnerable Python, PHP, Ruby, or Java runtimes?
* [ ] Is Python 2.x, PHP 5.x, or Java 7/8 still being used in production?

---

#### 💣 **23. Cryptographic Libraries with CVEs**

* [ ] Is vulnerable OpenSSL version (e.g., Heartbleed affected) still in use?
* [ ] Are outdated cryptographic libraries (e.g., Bouncy Castle, pycrypto) present?

---

#### 📁 **24. Mismanaged Software Repositories**

* [ ] Are software updates pulled from compromised or unofficial repos?
* [ ] Are custom package managers (e.g., conan, poetry, NuGet) misconfigured?

---

#### 🎭 **25. Signed Malware or Fake Packages**

* [ ] Are compromised libraries from typosquatting (e.g., `urllibs`, `reqeusts`) in use?
* [ ] Are packages verified with checksums or signed keys?

---

#### 📉 **26. Monitoring, Logging, or Security Agents**

* [ ] Are old ELK stacks, Prometheus, or Splunk agents installed?
* [ ] Are these exposing dashboards with known vulnerabilities?

---

#### 🗃️ **27. File Upload Tools with Known Issues**

* [ ] Are components like DropzoneJS, FineUploader using outdated insecure versions?
* [ ] Are file parsing tools (e.g., ExifTool, ImageMagick) outdated and RCE-prone?

---

#### 🎞️ **28. Media Libraries and Parsers**

* [ ] Is FFmpeg, libav, or imagemagick running vulnerable versions?
* [ ] Are audio/video uploads processed with exploitable binaries?

---

#### 🖥️ **29. Virtualization & Hypervisor Components**

* [ ] Are VMware, KVM, or VirtualBox tools outdated on host/guest machines?
* [ ] Are guest additions or extensions vulnerable to known guest-to-host escape CVEs?

---

#### 💻 **30. Dev Workstation Weak Links**

* [ ] Are developer machines using outdated IDE plugins with remote execution bugs?
* [ ] Are local dev environments (e.g., XAMPP, MAMP) exposing ports or using old components?

---

