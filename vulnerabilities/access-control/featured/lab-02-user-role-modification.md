# Lab 02 — User Role Can Be Modified in User Profile 

**Vulnerability:** Access Control — Insecure direct object manipulation / role modification  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how insufficient server-side authorization can allow users to escalate privileges by modifying their own role. By manipulating request parameters that should be server-controlled, an attacker can gain administrative access and perform restricted actions.

## Goal
- Identify whether the server enforces role integrity when users update their own profile.  
- Modify a user’s role via crafted requests in the controlled lab environment and access administrative functionality.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)
I logged in using the supplied credentials and accessed my account page. While updating the email address associated with my account, I noticed that the server response included my current role ID. I sent the email update request to Burp Repeater and modified the JSON request body to include `"roleid":2`. After resending the request, the server accepted the modification, and the response indicated my role had changed to 2. I then browsed to `/admin` and successfully deleted the user `carlos`. All sensitive information and identifiers were sanitized in saved artifacts.

---

## What I observed
- The server did not enforce role integrity when processing user profile updates.  
- Modifying the role ID in client-supplied JSON allowed privilege escalation from a normal user to an administrator.  
- Administrative functions were accessible immediately after the role change without further authentication.

---

## Proof (sanitized)
- Screenshot of the modified JSON request in Burp Repeater showing `"roleid":2`.  
- Admin UI screenshot after deleting `carlos` (sanitized).  


---

## Mitigation
- Enforce strict server-side validation of role assignments; users must not be able to modify their own role.  
- Implement role-based access control (RBAC) checks for all administrative endpoints.  
- Log and monitor privilege changes and reject any unauthorized role modifications.  
- Consider multi-step authorization for critical administrative actions.

---

## Lessons learned
- Client-side control over sensitive fields (like role IDs) can lead to privilege escalation.  
- Server-side authorization must validate every access attempt to critical functions.  
- Exposing role IDs in responses should be done cautiously and never relied upon as a security control.
