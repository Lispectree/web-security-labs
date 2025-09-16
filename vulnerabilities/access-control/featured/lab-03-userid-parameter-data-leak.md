# Lab 03 — User ID Controlled by Request Parameter with Data Leakage in Redirect

**Vulnerability:** Access Control — Insecure direct object reference / data leakage  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how insufficient authorization combined with user-controlled request parameters can lead to data leakage. By manipulating a user ID parameter, an attacker can access sensitive information belonging to another user, even when the application appears to redirect to a neutral page.

## Goal
- Verify whether the server enforces ownership checks when handling user-specific requests.  
- Exploit the vulnerability to obtain sensitive information (e.g., API keys) of another user in the controlled lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy 
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)
I logged in using the supplied credentials and accessed my account page. I sent the request to Burp Repeater and modified the `"id"` parameter to `carlos`. Although the server redirected me to the home page, the response body contained the API key belonging to `carlos`. I extracted this API key and submitted it to solve the lab. All sensitive information was sanitized in saved artifacts.

---

## What I observed (sanitized)
- The server did not validate that the user making the request actually owned the resource specified by the `"id"` parameter.  
- The response body leaked sensitive information (the API key) belonging to another user despite redirecting to the home page.  
- Access control based solely on client-supplied identifiers is insufficient.

---

## Proof (sanitized)
- Screenshot of the modified request in Burp Repeater with `"id": carlos`.  
- Response body showing the API key for `carlos` (sanitized).  


---

## Mitigation
- Enforce server-side ownership and authorization checks for all user-specific requests.  
- Never expose sensitive data in responses when the requester is not authorized to access it.  
- Consider using per-session tokens or opaque identifiers instead of predictable user IDs.  
- Log and monitor attempts to access other users’ data to detect potential abuse.

---

## Lessons learned
- User-controlled parameters referencing other users’ data can lead to sensitive information disclosure.  
- Redirects do not prevent data leakage if the response body contains sensitive content.  
- Access control must always be enforced server-side, independent of client input or UI behavior.
