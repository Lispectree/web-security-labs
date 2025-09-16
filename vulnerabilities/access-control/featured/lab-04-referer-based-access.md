# Lab 04 — Referer-Based Access Control

**Vulnerability:** Access Control — Insecure reliance on the HTTP Referer header  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how using the HTTP Referer header as a mechanism for access control is insecure. An attacker can manipulate or omit the Referer header to bypass restrictions, potentially performing administrative actions without proper authorization.

## Goal
- Understand whether access to administrative endpoints depends on the Referer header.  
- Bypass the Referer-based access control in the controlled lab environment to perform administrative actions.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history), Incognito/private browsing

---

## My steps (narrative, exactly what I did)
I logged in using admin credentials and accessed the admin panel to promote `carlos`, sending the HTTP request to Burp Repeater for inspection. Then, I opened a private browser window and logged in with a non-admin account. Browsing to `/admin-roles?username=carlos&action=upgrade`, I noticed that the request was blocked due to the absent Referer header. I then copied the non-admin user’s session cookie into the existing Burp Repeater request, changed the `username` parameter to my own account, and resent the request. The server accepted the request, allowing unauthorized privilege escalation. All sensitive information, including session identifiers.

---

## What I observed (sanitized)
- Access to administrative functionality was restricted based solely on the presence and value of the Referer header.  
- Omitting or forging the Referer header allowed a non-admin user to perform administrative actions.  
- Relying on client-supplied headers for access control is insecure and can be bypassed trivially.

---

## Proof (sanitized)
- Screenshot of the Burp Repeater request showing the modified session cookie and forged Referer header.  
- Admin UI screenshot after successfully performing the action (sanitized).  



## Mitigation
- Never rely on HTTP headers, including Referer, for access control.  
- Implement server-side authorization checks based on authenticated user roles and permissions.  
- Log attempts to access administrative endpoints and alert on suspicious activity.  
- Apply role-based access control (RBAC) consistently across all administrative actions.

---

## Lessons learned
- Client-controllable headers like Referer are easily spoofed and should not be trusted for authorization decisions.  
- Proper server-side authorization is essential to prevent privilege escalation.  
- Security mechanisms should be independent of client behavior and enforced on the server.
