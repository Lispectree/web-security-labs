# Lab 03 — Weak Isolation on Dual-Use Endpoint

**Vulnerability:** Business Logic — Weak isolation / insufficient authorization on shared endpoint
**Lab source:** PortSwigger Web Security Academy


---

## Short overview

This lab demonstrates how a single endpoint serving multiple purposes (dual-use) can allow privilege escalation if server-side authorization is weak or missing. By manipulating the request parameters, I was able to change another user’s password and take over their account.

## Goal

* Verify whether the password-change endpoint enforces the requirement to supply the current password and enforces the target username correctly.
* Use the endpoint to change an administrative account’s password in the controlled lab environment and confirm access.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps 

With Burp running, I logged in and accessed my account page, then changed my password once to observe the normal flow and capture the POST /my-account/change-password request; I sent that captured request to Burp Repeater and studied the request body, removed the current-password parameter entirely and forwarded the request (the server accepted the request and changed the password despite the missing current password), observed that the account changed was determined by the username parameter so I set username=administrator and provided a new password in the same request and sent it (the server accepted the change), logged out and then logged in as the administrator using the password I had set which succeeded, and finally visited the admin panel and deleted the target user carlos to solve the lab.
---****

## What I observed (sanitized)

* The password-change endpoint accepted requests without the `current-password` and performed the password update.
* The `username` parameter controlled which account was updated, enabling me to target the administrator account.
* The attack resulted in full account takeover of the administrator in the controlled lab.

---

## Proof (sanitized)

images/lab-03-weak-isolation.png)


---

## Mitigation

* Require server-side verification of the current password for password change operations, or use secure, single-use reset tokens tied to the account.
* Do not allow client-provided identifiers (like `username`) to determine which account is changed unless the server verifies the actor’s authorization to modify that account. Use session identity or server-side role checks instead.
* Apply centralized authorization checks across all endpoints so dual-use or hidden endpoints cannot bypass security controls.
* Log and alert on sensitive account modifications (especially admin account changes).

---

## Lessons learned

* Dual-use endpoints that accept client-supplied target identifiers are high-risk when server-side authorization is weak.
* UI-level protections are not sufficient — critical checks must be enforced server-side.
* Always validate the actor’s authority before performing sensitive state changes.
