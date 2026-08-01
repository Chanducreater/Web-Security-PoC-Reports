# Vulnerability Report: Parameter-Based Access Control via Cookie Manipulation

## 1. Executive Summary
- **Vulnerability Type:** Broken Access Control (Parameter-Based / Cookie Tampering)
- **OWASP Top 10 Category:** A01:2021 – Broken Access Control
- **Severity:** High
- **Target Endpoint:** `/admin`
- **Vulnerable Parameter:** Cookie `Admin` (`Admin=false` -> `Admin=true`)
- **Impact:** An attacker can tamper with client-side cookies to forge administrative privileges and perform unauthorized actions, such as deleting user accounts.

---

## 2. Vulnerability Description
The application determines user roles and administrative privileges based on a client-side controlled HTTP cookie (`Admin`). Because the backend trusts the value of this cookie without validating user identity or role session mappings server-side, any standard user can modify their cookie value from `Admin=false` to `Admin=true` to escalate privileges vertically.

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Log in to the application using standard user credentials (`wiener:peter`).
2. Intercept the HTTP Response or inspect browser cookies using Developer Tools (`F12`).
3. Locate the session cookie named `Admin`:
   ```http
   Set-Cookie: Admin=false; Path=/

   Modify the cookie value to true (Admin=true).

1.Navigate directly to the administrative endpoint:
GET /admin HTTP/2

2.Observe that the server grants full access to the Administrative Panel based solely on the forged cookie value.

3.Click Delete next to the user carlos to execute an unauthorized privilege operation.

## 4. 4. Remediation / Recommendation (How to Fix)
1.Never Trust Client-Side Data for Access Control: Do not store sensitive access control states (like roles or admin flags) in user-modifiable cookies, hidden fields, or URL parameters.

2.Server-Side Session State: Store user privileges securely on the server-side within the user's session state (e.g., mapped to a secure, cryptographically signed session ID/JWT).

3.Role-Based Access Control (RBAC): Perform server-side validation on every privileged endpoint checking if session.user.role == 'ADMIN' before authorizing sensitive actions.
