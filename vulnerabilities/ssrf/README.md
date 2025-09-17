# Server-Side Request Forgery (SSRF) — Featured Labs

## Overview

Server-Side Request Forgery (SSRF) occurs when an application fetches URLs or resources based on user-supplied input without properly validating or restricting the destination. SSRF can be used to access internal services, metadata endpoints, or other back-end systems that are not directly reachable from the internet, leading to sensitive data exposure or unauthorized actions.

I have completed many labs on the Web Security Academy. The three documented here were selected because they illustrate diverse SSRF techniques and bypasses: direct backend targeting, blacklist/encoding bypasses, and indirection via open redirects.

---

## Featured Labs

### 1. Basic SSRF against another back-end system

* **Objective:** Use a server-side URL parameter to reach an internal backend/admin interface.
* **Key technique:** Modify the `stockApi` parameter to point at internal IPs and iterate address parts to discover responsive hosts.
* **Lab file:** `lab-01-basic-ssrf-backend.md`

### 2. SSRF with Blacklist-Based Input Filter

* **Objective:** Bypass naive blacklist filters by using alternative IP representations and encoding tricks.
* **Key technique:** Use IP shorthand (e.g., `127.1`) or double-URL-encoding (e.g., `%2561`) to evade filters that perform single-pass decoding.
* **Lab file:** `lab-02-ssrf-blacklist-filter.md`

### 3. SSRF with Filter Bypass via Open Redirection Vulnerability

* **Objective:** Use an open redirect as an indirection vector so the server follows the redirect to an internal admin endpoint.
* **Key technique:** Craft a URL that leverages an open redirect (e.g., `/product/nextProduct?path=...`) and supply it to a server-side fetch parameter to reach internal services.
* **Lab file:** `lab-03-ssrf-open-redirect-bypass.md`

---

## Lessons and insights

* SSRF is powerful because it allows attackers to use the server as a proxy into internal networks.
* Blacklist-based protections are fragile — canonicalization, multiple encodings, and alternative address formats must be considered.
* Open redirects and other indirection vectors can bypass direct destination filters and should be treated as part of the attack surface.
* Effective defenses include strict allowlists, blocking private/internal IP ranges, full canonicalization and decoding before validation, and disabling unnecessary server-side fetch functionality.

---



