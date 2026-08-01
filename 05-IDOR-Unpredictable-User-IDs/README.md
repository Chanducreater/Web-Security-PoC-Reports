# Vulnerability Report: Insecure Direct Object Reference (IDOR) via Disclosed GUID

## 1. Executive Summary
- **Vulnerability Type:** Insecure Direct Object Reference (IDOR) / Horizontal Privilege Escalation
- **OWASP Top 10 Category:** A01:2021 – Broken Access Control
- **Severity:** High
- **Target Endpoint:** `/my-account?id=[GUID]`
- **Vulnerable Parameter:** `id`
- **Impact:** An attacker can obtain another user's GUID from public pages (e.g., blog posts) and access their private account details, exposing sensitive information such as API keys.

---

## 2. Vulnerability Description
Although the application uses Globally Unique Identifiers (GUIDs) to uniquely identify user accounts—preventing sequential ID guessing—it leaks these GUIDs in public application areas (blog comments/authors). Furthermore, the application lacks server-side authorization checks on the `/my-account` endpoint, allowing any logged-in user to view another user's account page simply by substituting the `id` parameter with the victim's GUID.

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Browse to a public blog post authored by or commented on by `carlos`.
2. Click on the user's name or inspect the link to extract `carlos`'s GUID:
   `https://[TARGET-LAB-ID].web-security-academy.net/blogs?userId=c1a2b3c4-...`
3. Log in with standard user credentials (`wiener:peter`).
4. Access your account page (`/my-account?id=[YOUR-GUID]`).
5. Replace your GUID in the `id` query parameter with `carlos`'s extracted GUID.
6. Observe that the server renders `carlos`'s private account dashboard without validating if the session user matches the requested `id`.
7. Extract the exposed API key to confirm complete account data compromise.

---

## 4. Remediation / Recommendation (How to Fix)
1. **Enforce Access Control Mapping:** The backend must check that the requesting user's session identifier matches the `id` parameter being accessed (`session.userId == request.getParameter("id")`).
2. **Indirect Object References:** Use session-based mappings (e.g., `/my-account` without any `id` parameter) so the server always fetches data for the currently authenticated user session.
3. **Prevent Sensitive ID Leaks:** Avoid publicly exposing internal unique IDs when standard display names or anonymous handles can be used instead.
