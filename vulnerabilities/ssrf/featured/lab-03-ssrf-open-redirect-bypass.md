# Lab 03 — SSRF with Filter Bypass via Open Redirection Vulnerability (featured)

**Vulnerability:** Server-Side Request Forgery (SSRF) — open redirect used to bypass destination filters
**Lab source:** PortSwigger Web Security Academy

---

## Short overview

This lab demonstrates how an open redirect in an application can be leveraged to bypass SSRF destination filters. When a server follows a redirect whose location is attacker-controlled, an attacker can cause the server to reach internal services indirectly, even if direct host values are blocked. By chaining the stock-check endpoint to an open redirect that points at an internal admin interface, the server follows the redirect and accesses internal functionality.

## Goal

* Verify whether the application blocks direct server-side requests to internal hosts via the `stockApi` parameter.
* Use an open redirection path as an indirection vector so the server follows a redirect to an internal admin endpoint and perform an administrative action in the controlled lab environment.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)
I visited a product page and clicked "Check stock" while intercepting the request in Burp Suite. I sent the intercepted request to Burp Repeater and attempted to tamper with the `stockApi` parameter to point the server at a different host; this was blocked. I then navigated to the "next product" functionality and observed that the `path` parameter was placed into the Location header of a redirect response, creating an open redirect. I crafted a URL that used the open redirect to point at an internal admin interface, for example `/product/nextProduct?path=http://192.168.0.12:8080/admin`, and supplied this URL to the `stockApi` parameter of the stock checker. The stock checker followed the redirect and showed the admin page. I then amended the path to call the deletion endpoint `/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos` and the server performed the admin action. 

## What I observed (sanitized)

* Direct tampering of the `stockApi` destination was blocked, but an open redirect elsewhere in the application allowed indirect access to arbitrary URLs.
* The server followed the redirection chain and accessed the internal admin interface, effectively bypassing SSRF destination filtering.
* Using the open redirect as an indirection vector permitted invoking internal admin actions such as deleting `carlos`.

---

## Proof (sanitized)

* Repeater request showing `stockApi` set to `/product/nextProduct?path=http://192.168.0.12:8080/admin` and the resulting response containing admin UI content (sanitized).
* Repeater request and response for `/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos` demonstrating the deletion action (sanitized).


---

## Mitigation

* Avoid following redirects to locations derived from user-controlled input. Validate and restrict redirect locations or disallow open redirects entirely.
* For server-side fetch functionality, implement strict allowlists and block indirect redirection chains that lead to internal hosts.
* Canonicalize and validate URLs before following them; disallow private or loopback IP addresses and unexpected ports.
* Log and monitor outbound server-side requests and redirects to detect unusual internal access patterns.

---

## Lessons learned

* Open redirects are powerful indirection vectors when combined with server-side fetch features; they can be used to bypass direct destination filtering.
* Defenses against SSRF should consider indirect paths (redirects) as well as direct host values.
* Canonicalization, allowlisting, and blocking of private IP ranges are important safeguards to prevent SSRF via redirection.
