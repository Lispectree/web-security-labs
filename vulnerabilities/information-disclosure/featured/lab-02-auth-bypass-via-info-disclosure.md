# Lab 02 — Authentication Bypass via Information Disclosure

**Vulnerability:** Information Disclosure — sensitive data leak enabling authentication bypass  
**Lab source:** PortSwigger Web Security Academy (controlled lab example)  


---

## Short overview
This lab shows how an information disclosure behavior (in this case, an application appending an internal trust header based on client IP) can be abused to bypass authentication. By leveraging the TRACE method to observe how the server reflects or appends an `X-Custom-IP-Authorization` header and then using Burp's Match-and-Replace feature to inject a local IP value, an attacker can make their requests appear to originate from localhost and gain admin access.

## Goal
- Discover whether the application uses a header or other value derived from client IP to grant privileged access.  
- Use an information-disclosure technique to learn how the server determines local requests and then forge that indicator to bypass authentication and access the admin panel.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, HTTP history, Repeater, Match-and-Replace)

---

## My steps (narrative, exactly what I did)
I sent a normal request to the admin page and inspected the response in Burp Repeater; the response indicated that the admin panel is accessible only when logged in as an administrator or when the request originates from a local IP. To understand how the server determines a local request, I resent the request using the TRACE method and examined the returned message. The TRACE response showed that the server automatically appended an `X-Custom-IP-Authorization` header containing my IP address; this header is apparently used by the server to decide whether the request came from localhost. Knowing that control point, I opened Burp Proxy → Match and Replace and added a new request header replacement rule. I left the Match field empty, selected `Request header` as the Type, and set the Replace field to `X-Custom-IP-Authorization: 127.0.0.1`. I tested the rule and verified the auto-modified request preview showed the header added. With this rule active, Burp injected `X-Custom-IP-Authorization: 127.0.0.1` into outgoing requests automatically. I then browsed to the site’s home page and noticed the application treated my session as originating from localhost; the admin panel became accessible in the UI. I used the admin interface to delete the user `carlos` to solve the lab. All artifacts saved were sanitized to remove real IPs and session identifiers.

---

## What I observed 
- The server relied on an `X-Custom-IP-Authorization` header (derived from client IP) to decide whether a request is local and therefore allowed privileged access.  
- Using TRACE revealed that the application reflects or appends that header containing the current client IP, providing the attacker with the exact header name and expected format.  
- By forcing Burp to add `X-Custom-IP-Authorization: 127.0.0.1` to requests, the application accepted the request as if it originated from localhost and returned admin functionality.

---

## Proof 


---

## Mitigation
- Never rely on client-controllable headers or client-provided values (including reflected headers) to make security-critical decisions such as granting administrative access.  
- Disable HTTP methods that are not required in production (e.g., TRACE) when they are not needed, to prevent information-leaking reflections.  
- Perform server-side validation of client IPs using reliable server-side network stack information (not request headers) and restrict privilege elevation to authenticated and authorized session state.  
- Add server-side checks that ignore or override client-supplied headers used for internal trust decisions and log/alert on unexpected header values.  
- Harden configuration to prevent header reflection and ensure any internal trust indicators are not exposed to remote clients.

---

## Lessons learned
- Information disclosure can reveal the exact control point an attacker needs (header name/format) to forge authentication indicators.  
- TRACE and other reflective HTTP methods can be leveraged to learn server-side behaviors; such methods are often safe to disable in production.  
- Security decisions must be based on server-validated state and not on client-controllable inputs or reflected values.

