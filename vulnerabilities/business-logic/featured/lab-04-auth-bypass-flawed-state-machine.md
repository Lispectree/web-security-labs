# Lab 04 — Authentication Bypass via Flawed State Machine 

**Vulnerability:** Business Logic — Authentication bypass / flawed state machine
**Lab source:** PortSwigger Web Security Academy


---

## Short overview

This lab shows how an application that relies on a fragile state machine during authentication and role selection can be abused. By interrupting or dropping a specific request in the login flow, the application state defaults to a privileged role (administrator) and grants access to administrative functionality without proper authorization.

## Goal

* Confirm whether the application relies on an unenforced client-driven state transition (role selection) to grant administrative access.
* Exploit the flawed state machine to obtain admin access in the controlled lab environment and confirm by performing an admin action.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, HTTP history, Intercept)

---

## My steps (narrative, exactly what I did)

I started the login flow with Burp proxy enabled and completed the sign-in process, noticing the role-selection step that normally appears before the application takes me to the home page; after finding the `/admin` path via content discovery, I attempted to open `/admin` directly from the role-selection screen and saw it was blocked; I then returned to the login page, enabled Burp interception, and proceeded to submit the `POST /login` request which progressed to a `GET /role-selector` request — while intercepting I intentionally dropped the `GET /role-selector` request and then navigated to the application home page; because the role-selection transition never completed, the application defaulted my session into the administrator role and I could access the admin panel; finally I used the admin UI to delete the user `carlos` to solve the lab.

---

## What I observed 

* The application enforces role selection through a client-visible step but does not robustly tie that selection to server-side authorization state.
* Dropping the role-selection request caused the server to assume a default or fallback role that was overly privileged (administrator).
* This behavior allowed access to the admin interface and completion of administrative actions in the controlled lab.

---

## Proof

images/lab-04-auth-bypass.png)



---

## Mitigation

* Enforce server-side role and authorization checks independent of client-side navigation/state transitions.
* Do not rely on the presence or sequence of client requests for critical authentication/authorization decisions — derive role from server-validated session attributes.
* Implement explicit, server-side state transitions that are atomic and resistant to interruption; require re-validation for elevation to privileged roles.
* Log and alert on anomalous authentication flows and unexpected state changes (e.g., role changes without explicit selection).

---

## Lessons learned

* Relying on a client-driven state machine for authentication or authorization decisions is dangerous.
* Interrupting or manipulating request sequences can cause an application to enter insecure default states.
* Critical access controls must be enforced and validated on the server side, not inferred from client navigation.

