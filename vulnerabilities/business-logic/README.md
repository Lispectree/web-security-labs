# Business Logic — Featured Labs 

## Overview

**Business logic vulnerabilities arise when an application’s intended workflows, state machines, or trust assumptions are implemented incorrectly. Unlike purely technical bugs, these flaws exploit the way features are designed and combined — for example, relying on client-side state, inconsistent checks across flows, or dual-use endpoints that trust client-supplied identifiers. The four labs documented here illustrate common real-world classes of business-logic problems and practical exploitation and mitigation techniques.
I have completed many more labs on the Web Security Academy, but the four documented in this folder were chosen because they together reflect a diverse and practical set of business-logic issues that are commonly encountered in real-world applications.

## Featured Labs

### 1. Client-side Trust

* **Objective:** Demonstrate how trusting client-side controls or assumptions can be abused to change application behavior or access.
* **Key technique:** Manipulating client-side state, parameters, or UI flows to bypass restrictions enforced only in the browser.
* **Lab file:** `lab-01-client-side-trust.md`

### 2. Inconsistent Security Controls

* **Objective:** Show how different application flows applying different validation/authorization rules can be chained to bypass protections.
* **Key technique:** Exercise alternate flows and combine them to reach functionality that is insufficiently protected in one path.
* **Lab file:** `lab-02-inconsistent-security-controls.md`

### 3. Weak Isolation on Dual-Use Endpoint

* **Objective:** Exploit an endpoint that serves multiple purposes and accepts client-supplied target identifiers to modify another user’s data.
* **Key technique:** Remove or tamper with parameters (e.g., omit current password or change `username`) so the server performs sensitive actions on a victim account.
* **Lab file:** `lab-03-weak-isolation.md`

### 4. Authentication Bypass via Flawed State Machine

* **Objective:** Abuse fragile authentication/role-selection flows to obtain elevated privileges.
* **Key technique:** Interrupt or manipulate the request sequence (e.g., drop or replay a transition request) so the server falls back to an insecure default state (administrator) or otherwise misassigns roles.
* **Lab file:** `lab-04-auth-bypass-flawed-state-machine.md`

---

## Lessons learned

* Business-logic flaws often stem from incorrect assumptions about the client, sequence of operations, or ownership of resources.
* UI-only protections or client-side checks are not sufficient — critical authorization and state transitions must be validated server-side.
* Inconsistent enforcement across different flows is a high-value attack surface: attackers will look for alternate paths that lack the same checks.
* Dual-use endpoints and fragile state machines are particularly risky because small interruptions or malformed requests can produce privileged outcomes.

---

## Tools used

* Browser
* Burp Suite (Proxy, Repeater, HTTP history / Intercept / Intruder)

---



