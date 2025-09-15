# Lab 02 — SQL injection vulnerability allowing login bypass (summary)

**Type:** In-band SQL Injection — Authentication bypass

**Goal:** Verify whether the login form accepts input that can alter authentication queries and allow unauthorized access.

**Environment & tools:** PortSwigger Web Security Academy (lab); Browser + HTTP proxy (Burp Suite) to intercept and modify the login request.

**What I did (sanitized):**
1. Intercepted the login HTTP request with Burp Suite while submitting normal credentials.
2. Modified the `username` field in the intercepted request to a crafted value that alters the backend query logic (used a payload that effectively neutralized the remainder of the authentication check).
3. Forwarded the modified request and observed the authentication result.

**What I observed:**  
The modified request caused the application to accept the login attempt (changed from "invalid credentials" to "authenticated"), indicating the login input was incorporated insecurely into the authentication query.

**Mitigation:**  
- Use parameterized queries / prepared statements for authentication checks (never concatenate user input into SQL).  
- Implement account lockout and multi-factor authentication as defense-in-depth.  
- Avoid verbose login error messages that reveal implementation details.



