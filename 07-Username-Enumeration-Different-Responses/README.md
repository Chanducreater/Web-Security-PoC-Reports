# Vulnerability Report: Username Enumeration via Different Responses

## 1. Executive Summary
- **Vulnerability Type:** Username Enumeration & Password Brute-Force
- **OWASP Top 10 Category:** A07:2021 – Identification and Authentication Failures
- **Severity:** Medium / High
- **Target Endpoint:** `/login`
- **Vulnerable Parameters:** `username`, `password`
- **Compromised Credentials:** `athena : 1234`
- **Impact:** An attacker can distinguish valid usernames from invalid ones based on server responses, enabling targeted credential brute-forcing and full account takeover.

---

## 2. Vulnerability Description
The authentication mechanism behaves inconsistently when handling failed login attempts. Submitting an invalid username returns a distinct error message (`Invalid username`), whereas submitting a valid username with an incorrect password returns `Incorrect password`. This behavior allows an attacker to enumerate registered accounts before attempting a targeted dictionary attack on valid passwords.

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Captured the `POST /login` request using a local HTTP proxy.
2. Iterated through a wordlist of candidate usernames while keeping the password parameter static.
3. Observed server responses: requests with invalid usernames returned `Invalid username`, while the valid user `athena` yielded `Incorrect password` (resulting in a distinct response length).
4. Fixed the valid username as `athena` and iterated through a wordlist of candidate passwords.
5. Located the password `1234`, which produced an HTTP `302 Found` response (redirection to `/my-account`).
6. Successfully logged in using `athena:1234` to complete the lab.

---

## 4. Remediation / Recommendation (How to Fix)
1. **Generic Error Messages:** Ensure login failures return identical messages (e.g., `"Invalid username or password"`) regardless of whether the username or password was incorrect.
2. **Uniform HTTP Responses:** Ensure the HTTP status code, response body size, and execution timing remain consistent across all authentication failure scenarios.
3. **Rate Limiting & Account Lockout:** Implement IP rate-limiting and temporary account lockouts after multiple consecutive failed login attempts.
