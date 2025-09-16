# Lab 02 — Inconsistent Security Controls (featured)

**Vulnerability:** Business Logic — Inconsistent access controls across endpoints  
**Lab source:** PortSwigger Web Security Academy  

---

## Short overview
This lab shows how inconsistently enforced authorization across different application paths can be abused. An endpoint that performs an administrative function existed but required a specific email domain; by registering and then changing my account email to that domain, I gained access to admin functionality that should have been restricted.

## Goal
- Discover hidden or unlinked application paths.  
- Use an alternate workflow to obtain the privileges required to access an administrative endpoint (lab-only).

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Site map, Engagement tools → Discover content, HTTP history, Repeater) and the lab email client.

---

## My steps (narrative, sanitized)

I started by exploring the application with Burp Suite. From **Target → Site map**, I launched the content discovery tool (Engagement tools → Discover content) and started a passive discovery run. After a short scan I noticed an `/admin` path had been discovered.

I tried browsing to `/admin` directly in the browser, but the UI refused access; the error page hinted that users from a particular company domain had privileges.

To get a path into that privileged group, I went to the account registration page and read the instructions — the application suggested employees should use a company email. I opened the lab email client to discover my temporary lab email domain (the `...web-security-academy.net` address shown by the client).

I registered a new account using an arbitrary local part at my lab email domain (e.g., `anything@<my-lab-domain>`). After requesting confirmation I used the lab’s email client and clicked the confirmation link to complete registration.

I logged in with the new account and navigated to **My account**. There I saw the option to change my email address. I edited my account email and set it to an address under the `dontwannacry.com` domain (the value the admin check expected). After saving that change I revisited `/admin`.

This time, the admin panel loaded and I had the administrative UI available. Using the admin interface I deleted the target account (Carlos).

---

## What I observed (sanitized)
- The site map discovery revealed an `/admin` path that was not exposed in normal navigation.  
- The `/admin` endpoint enforced a domain-based privilege check tied to the user’s email, rather than a robust role/permission check.  
- By registering and then changing my email to the privileged domain, I could elevate my access and perform admin actions.  
- The issue was reproducible and depended on inconsistent enforcement of security controls across registration, account update, and admin endpoints.

*(Add sanitized proof image: `images/lab-02-inconsistent-controls.png` — show the discovered `/admin` listing, the account email change, and the admin UI showing the delete action; redact any sensitive tokens or unique lab identifiers.)*

---

## Mitigation
- Enforce server-side authorization independent of client-supplied attributes (e.g., email value). Membership in privileged groups should be tied to trusted, server-controlled attributes (role flags, SSO claims) and not just an email domain submitted by the user.  
- Validate and authorize every sensitive action at the server, using a central access-control policy so checks are consistent across all endpoints.  
- Treat internal/unlinked endpoints as production APIs — require the same rigorous auth checks and logging.  
- Implement monitoring and alerts for newly privileged accounts or sudden privilege changes.

---

## Lessons learned
- Client-visible hints (email instructions, UI messages) can reveal business logic checks — attackers can abuse flows (register → update profile) to meet those checks.  
- Discovering unlinked endpoints via site-map/content discovery is a powerful reconnaissance step.  
- Consistent, server-side authorization is essential: never rely on UI constraints, email format, or client-controlled values as the sole gate to privileged functionality.

