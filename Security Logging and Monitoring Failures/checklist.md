## **Security Logging & Monitoring Failures – Offensive Security Checklist**

**1–20 (Core)**

1. Verify whether **authentication failures** (wrong password, invalid token) are being logged.
2. Check if **authorization failures** (forbidden access attempts) are recorded in the logs.
3. Test whether **sensitive transactions** (password reset, role changes) are logged with enough detail.
4. Look for **privilege escalation attempts** in logs.
5. Confirm whether **admin panel logins** are logged with IP, timestamp, and user details.
6. Attempt brute force login and check if it triggers **alerts** or **lockouts**.
7. Test whether **critical API calls** (like DELETE, PATCH, UPDATE) are logged.
8. Verify whether logs store **source IP addresses** and **User-Agent strings** for traceability.
9. Confirm whether failed **input validation attempts** are logged.
10. Test if **application errors** are logged with sufficient details (stack traces for dev, sanitized for prod).
11. Check whether **database query errors** are logged (potential SQL injection attempts).
12. Verify whether **file upload attempts** (successful and failed) are recorded.
13. Look for **malicious file execution** logging.
14. Check if **system-level commands** executed via the app are logged.
15. Verify whether **login from new devices/locations** is detected and logged.
16. Test whether **session hijacking attempts** trigger logs.
17. Check for **alerting on suspicious patterns**, like too many failed logins in a short time.
18. Verify whether **time-synced logging** is in place across services (NTP).
19. Check if **critical security logs** are **write-once** or tamper-proof.
20. Confirm whether logs are **protected from unauthorized access**.

---

**21–40 (Advanced & Red Team Focus)**
21\. Test whether **API key misuse** is logged.
22\. Verify whether **JWT token signature failures** are recorded.
23\. Check if **expired token use** is logged.
24\. Test whether **invalid CSRF token attempts** appear in logs.
25\. Look for **unusual download patterns** in logs (possible data exfiltration).
26\. Test whether **DNS queries to suspicious domains** are logged.
27\. Check whether **multiple account creations from same IP** are flagged.
28\. Test if **unexpected HTTP methods** (PUT, TRACE) are logged.
29\. Verify whether **large POST/GET requests** are logged.
30\. Look for **log gaps** (missing logs during attack simulation).
31\. Check if **2FA bypass attempts** are recorded.
32\. Test whether **failed file integrity checks** appear in logs.
33\. Verify whether **rate-limiting triggers** are logged.
34\. Test for **unexpected API endpoints being hit** and their logging.
35\. Check whether **direct object reference exploitation attempts** are logged.
36\. Test whether **XML external entity (XXE)** parsing errors are logged.
37\. Verify whether **command injection attempts** are captured.
38\. Check if **HTTP request smuggling anomalies** appear in logs.
39\. Test whether **access to deprecated APIs** is logged.
40\. Verify whether **server resource spikes** (CPU, RAM) from suspicious activity are logged.

---

