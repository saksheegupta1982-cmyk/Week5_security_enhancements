# Week 5 - Security Audit & Hardening Report
*Project: Secure Python CLI Login and Notes Application
*Student: Sakshi Ramchandra Gupta
*Task: Security Enhancement - Vulnerability Assessment and Remediation

## 1. Initial Security Review
Application: CLI based Login and Notes App using sqlite3
Tool Used: Bandit (pip install bandit), Manual Code Review

Bandit Command: bandit -r app_vulnerable.py Found 5 High Severity Issues.

##2. Vulnerability Identified  and Exploitation Scenario

1. SQL Injection (Critical) 
Query: f"SELECT * FROM users WHERE username = '{username}'"
Exploit: User inputs 'OR' 1'=' 1 -> Bypasses 
login without password, can dump full DB.

2.Hardcoded Secret(High) 
SECRET_KEY = "admin123"
Exploit: If code goes to GitHub, attacker gets admin access.

3. Improper Error Handling/Information Disclosure(Medium)
print(f"Error:{e}")shows file path and stack trace.
Exploit: Attacker learns internal file structure for furthr attacks.

4.Path Traversal(High)
open(f"{username}_notes.txt")-If username is ../../etc/password,can read system files.

5. Insecure Password Storage(Critical)
Storing plain text password.
Exploit: If DB leaks, all usser passwords compromised.

## 3. Security Hardening Implemented 
1. Parameterized Queries: Used? Placeholders to prevent SQLi.

2.ENV Variable: os.getenv for secrets.

3.Input Sanitization: Regex ^[a-zA-Z0-9_]+$+os.path.join to block traversal.

4. Secure Error Handling: Generic messages like "Invalid credentials".

5. Encryption & Hashing: Used bcrypt for password hashing, getpass for input.

## 4.Before vs After

| Vulnerability | Vulnerable Code | Secure Fix | 
|---|---|---|
| SQLi | f-string query | cursor.execute("...?",(user,))|
| Hardcoded Key | SECRETE = " admin123" | os.getenv() |
| Error Leak | print(e) | print("Invalid credentials") |
| Path Traversal | open(username) | os.path.join+sanitize |
| Plain Password | INSERT password | bcrypt.hashpw() |

## 5. Testing & Validation
Test 1: Input  'OR'1'='1 -> Secure app blocks it.
Test 2: Username = ../../etc/passwd ->Blocked by sanitize_input()
Test 3: Bandit scan on secure file -> 0 High issues.
Functionally maintained: Login and notes still work after fixes...

## 6. Conclusion
All 5 identified  vulnerabilities were successfully fixed without breaking core functionality. The application now follows OWASP Secure Cding Practices, uses parameterized queries, secure error handling, input validaton, and strong hashing. The secure version is production - ready from a basic security perspective.  



