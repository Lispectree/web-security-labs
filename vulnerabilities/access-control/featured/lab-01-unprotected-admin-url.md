# Lab 01 — Unprotected Admin Functionality with Unpredictable URL (featured)

**Vulnerability:** Access Control — Hidden or unprotected administrative endpoints  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how administrative functionality may be exposed even when the URL is not easily guessable. Hidden endpoints that rely on obscurity rather than proper authentication or authorization controls can be discovered by inspecting client-side code or resources. Exploiting such endpoints allows unauthorized users to perform administrative actions.

## Goal
- Discover hidden administrative functionality by analyzing the application’s client-side code or network responses.  
- Access the unprotected admin panel in the controlled lab environment and perform administrative actions, such as deleting a user.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, HTTP history, Developer Tools)

---

## My steps 
I reviewed the home page’s source code using Burp Suite the browser’s developer tools can also be used. While examining the JavaScript, I noticed it contained the URL of the admin panel. Using that URL, I navigated directly to the admin interface. Once inside, I performed the administrative action of deleting the user `carlos` to solve the lab. All sensitive data was sanitized before saving any artifacts.

---

## What I observed
- The admin panel was accessible via a URL disclosed in client-side JavaScript.  
- No authentication or additional access controls prevented access to the administrative functionality.  
- Hidden URLs alone are insufficient as an access control mechanism.

---

## Proof (sanitized)
- Screenshot of the relevant JavaScript snippet showing the admin panel URL (sanitized).  
- Admin UI screenshot after deleting `carlos` (sanitized).  



---

## Mitigation
- Enforce proper authentication and authorization on all administrative endpoints, regardless of URL obscurity.  
- Never rely on hidden or unpredictable URLs as a security measure.  
- Apply role-based access control (RBAC) and server-side checks to ensure only authorized users can access admin functionality.  
- Log and monitor access attempts to administrative pages for auditing and anomaly detection.

---

## Lessons learned
- Security through obscurity (unpredictable URLs) is not a substitute for proper access control.  
- Administrative endpoints should be protected by robust authentication and authorization checks at the server level.  
- Client-side code can inadvertently leak sensitive information to attackers.
