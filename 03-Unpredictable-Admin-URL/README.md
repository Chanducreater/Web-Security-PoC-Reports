# Vulnerability Report: Unprotected Admin Functionality via Disclosure in Client-Side JavaScript

## 1. Executive Summary
- **Vulnerability Type:** Broken Access Control (Unprotected Admin Functionality / Information Disclosure)
- **OWASP Top 10 Category:** A01:2021 – Broken Access Control
- **Severity:** High
- **Target Endpoint:** Obfuscated Administrative Panel (e.g., `/admin-iey1iv`)
- **Impact:** An attacker can discover obfuscated administrative paths embedded in client-side code and execute sensitive actions, such as deleting user accounts.

---

## 2. Vulnerability Description
The web application relies on "Security through Obscurity" by assigning a randomized, unpredictable URL to its administrative panel. However, the client-side JavaScript code exposes this administrative path to all users, regardless of their role or authentication status. Furthermore, the backend fails to validate server-side permissions upon direct navigation, allowing unauthorized users full access to administrative functions.

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Navigate to the application home page in the browser.
2. View the page source (`Ctrl + U`) or inspect client-side JavaScript files.
3. Locate the script responsible for rendering UI elements based on user roles:
   ```javascript
   var isAdmin = false;
   if (isAdmin) {
       var adminPanelTag = document.createElement('a');
       adminPanelTag.setAttribute('href', '/admin-iey1iv');
       adminPanelTag.innerText = 'Admin panel';
   }


  ## 4. Remediation / Recommendation (How to Fix)
Never rely on Obscurity: Do not consider randomized or obfuscated URLs as a security measure.

Server-Side Authorization Checks: Enforce robust Access Control on the backend for all sensitive routes, ensuring user.role == 'ADMIN' before rendering pages or executing actions.

Remove Sensitive Endpoints from Client-Side Code: Avoid leaking administrative routes, logic, or API endpoints in client-side HTML or JavaScript files accessible to unauthenticated or low-privileged users.
