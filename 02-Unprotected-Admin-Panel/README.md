# Vulnerability Report: Unprotected Admin Functionality via Disclosure in robots.txt

## 1. Executive Summary
- **Vulnerability Type:** Broken Access Control (Unprotected Administrative Functionality)
- **OWASP Top 10 Category:** A01:2021 – Broken Access Control
- **Severity:** Critical
- **Target Endpoint:** `/administrator-panel`
- **Impact:** An unauthenticated attacker can discover sensitive administrative interfaces and perform privileged actions, such as deleting arbitrary user accounts (`carlos`).

---

## 2. Vulnerability Description
The application hosts administrative functionalities at an unlinked URL (`/administrator-panel`). While this URL is not linked directly in the standard user interface, it is publicly disclosed inside the site's `robots.txt` file under the `Disallow` directive. Furthermore, the backend fails to validate user roles or session permissions when the endpoint is requested directly, allowing any unauthenticated user full administrative privileges.

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Open the target web application in the browser.
2. Navigate to `/robots.txt` by appending it to the root domain:
   `GET /robots.txt HTTP/2`
3. Identify the disclosed admin path from the response:
   ```text
   User-agent: *
   Disallow: /administrator-panel

Copy /administrator-panel and paste it directly into the browser's address bar.

Observe that the Administrative Panel loads without requiring any authentication or role checks.

Click the Delete button next to the target user account (carlos) to perform an unauthorized administrative action.

HTTP Request (Direct Admin Access):
GET /administrator-panel HTTP/2
Host: [TARGET-LAB-ID].web-security-academy.net
User-Agent: Mozilla/5.0

HTTP Response:
   HTTP/2 200 OK
Content-Type: text/html; charset=utf-8

...
<div>
    <span>carlos</span>
    <a href="/administrator-panel/delete?username=carlos">Delete</a>
</div>
...

## 4. Remediation / Recommendation (How to Fix)
To resolve this Broken Access Control vulnerability, the development team should implement:

Role-Based Access Control (RBAC): Ensure every sensitive request (especially under administrative routes) performs server-side role validation checking if user.role == 'ADMIN'.

Do Not Rely on Secrecy: Never rely on hiding URLs or listing sensitive paths in robots.txt as a security mechanism (Security through Obscurity).

Deny by Default: Enforce authentication and authorization checks at the routing layer before serving administrative pages.
   
