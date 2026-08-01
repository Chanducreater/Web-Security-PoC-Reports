# Vulnerability Report: Horizontal to Vertical Privilege Escalation via IDOR & Password Disclosure

## 1. Executive Summary
- **Vulnerability Type:** Horizontal to Vertical Privilege Escalation / Sensitive Data Exposure
- **OWASP Top 10 Category:** A01:2021 – Broken Access Control & A02:2021 – Cryptographic Failures
- **Severity:** Critical
- **Target Endpoint:** `/my-account?id=administrator`
- **Impact:** An attacker can perform IDOR to view high-privileged accounts, extract cleartext/masked passwords rendered in HTML source, take over the administrator account, and execute administrative functions.

---

## 2. Vulnerability Description
The application exhibits an Insecure Direct Object Reference (IDOR) vulnerability on the `/my-account` page. It allows low-privileged users to request account details of any user by modifying the `id` parameter. Additionally, the application leaks the account's existing password inside the HTML input field (`<input type="password" value="...">`). Combining IDOR with this sensitive data disclosure enables a low-privileged attacker to compromise the administrator account (Vertical Escalation).

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Log in to the application with standard user credentials (`wiener:peter`).
2. Access your account page and observe the URL structure:
   `GET /my-account?id=wiener HTTP/2`
3. Tamper with the `id` query parameter to target the administrative user:
   `GET /my-account?id=administrator HTTP/2`
4. Inspect the HTTP Response / HTML Page Source (`Ctrl + U`) and locate the password input element:
   ```html
   <input type="password" name="password" value="[ADMIN_PASSWORD_HERE]">
