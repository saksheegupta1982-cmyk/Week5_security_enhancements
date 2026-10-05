# Week 5 - Security Enhancement & Audit - Login + Notes App
* Domain: Python Secure Development *

# 1. Project  Overview
This project is a CLI- based Login + Notes Management Application built in Python using 'sqlite3'. The goal of Week 5 was to  perform a security audit on a vulnerable version of the app, identify OWASP Top 5 issues, and implement secure coding practices.

we have maintained two versions for comparison:
- 'app_vulnerable.py' - Contain 5 intentional vulnerabilities for audit purposes
- 'app_secure.py' - Hardened version with fixes

# 2. Vulnerabilities Identified in Vulnerable Version
 
During manual review and Bandit scan (`bandit -r app_vulnerable.py`),5 major issues were found:

1. *SQL  Injection (Critical - OWASP A03:2021):*uses f-string `f"SELECT * FROM...'{username}'"` - Allows `' OR ' 1'='1` payload to bypass login.
2. *Hardcoded Secret (High - OWASP  A07:2021):* `SECRET_KEY = "admin123"` - If pushed to GitHub, leaks admin access.
3. *Improper Error Handling (Medium):* `print(f"Error: {e}")` - Leaks internal files paths and stack trace.
4. *Path Traversal (High - OWASP A01:2021):* 'open(f"{username}_notes.txt")'- Input like '../../etc/passwd' can read system files.
5. *Insecure Password Storage (Critical):* stores plain text password - On DB leak, all passwords compromised.

# 3. Security Fixes Implemented in Secure Version

1. *Parameterized Queries:* Replaced f-strings with `cursor.execute("SELECT * FROM username =?", (username,))` - DB driver auto-escapes input.
2. *Environment Variables:* `SECRET_KEY = os.getenv("App_SECRET_KEY")`
3. *Input Sanitization:* Added `sanitize_input()` with regex `^[a-zA-Z0-9_]+$` + `os.path.join()` to block traversal.
4. *Generic Error Messages:* Changed all errors to `Invalid credentials` / `No notes found` - Prevents User Enumeration and Info Disclosure.
5 .*Secure Hashing:* Used `bcrypt.hashpw()` and `bcrypt.checkpw()` with salt + `getpass` for hidden input.

# 4. How to Run & Rplicate Audit

**Prerequisites:**
```bash
pip install -r requirements.txt
#or
pip install bcrypt bandit 