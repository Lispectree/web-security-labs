# Lab 01 — Basic SSRF against another back-end system (featured)

**Vulnerability:** Server-Side Request Forgery (SSRF) — SSRF to internal backend/admin interface
**Lab source:** PortSwigger Web Security Academy


---

## Short overview

This lab demonstrates a basic Server-Side Request Forgery (SSRF) where an attacker can control a parameter used by the server to fetch an external URL. By manipulating the `stockApi` parameter, it's possible to make the server request an internal backend service (for example, an admin interface) and interact with internal endpoints not normally reachable from the public internet.

## Goal

* Confirm whether a parameter controlling server-side requests (e.g., `stockApi`) can be used to reach internal backend services.
* Use SSRF to access an internal admin endpoint and perform an administrative action (delete `carlos`) in the controlled lab environment.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Intruder, Repeater)

---

## My steps (narrative, exactly what I did)

I opened a product page and clicked the "Check stock" button while Burp Suite was intercepting requests. I sent the intercepted request to Burp Intruder and modified the `stockApi` parameter to point at an internal host: `http://192.168.0.1:8080/admin`. I set the payload position to iterate the final octet of the IP address by highlighting the final digit and adding it as a payload marker. In the Payloads panel I chose the Numbers payload type and configured it to iterate values `1, 255, 1` (from 1 to 255 with step 1). I started the attack and sorted the results by status code; one request returned HTTP 200 and indicated an admin interface. I sent that request to Burp Repeater and modified the `stockApi` path to admin deleting a user, then forwarded the request to trigger deletion of a user.

---

## What I observed 

* The server used the `stockApi` parameter to perform server-side HTTP requests without validating or restricting the destination.
* Iterating the final octet revealed an internal host responding on port 8080 with an admin interface (HTTP 200).
* Using SSRF to call `/admin/delete?username=carlos` caused the server to perform the administrative action, allowing deletion of `carlos` from the internal service.

---

## Proof (sanitized)

* Burp Intruder results showing a request with HTTP 200 status for an internal host (redacted IP).
* Repeater request and response for `GET /admin/delete?username=carlos` demonstrating the deletion action (sanitized).



---

## Mitigation

* Do not allow user-controlled input to determine server-side request destinations. Validate and whitelist allowed hostnames/domains and ports for any server-side URL fetch functionality.
* Implement allowlists for external resources; block private/internal IP ranges from being requested by server-side fetchers.
* Apply strict input validation and canonicalization on URL parameters and restrict schemes (e.g., only allow `https` to known domains).
* Add request-validation middleware that prevents SSRF by checking resolved IPs against a denylist of internal ranges.
* Log and monitor outbound server-side requests to detect unexpected internal access patterns.

---

## Lessons learned

* SSRF can be used to reach internal services that are otherwise inaccessible from the internet.
* Iterative scanning of hostname/IP components can reveal internal hosts responding on different ports or paths.
* Whitelisting and validation of server-side fetch destinations are essential to prevent SSRF-based internal recon and exploitation.
