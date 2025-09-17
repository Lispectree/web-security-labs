# Race Conditions — Featured Labs

## Overview

Race conditions occur when an application performs non-atomic operations — for example, a separate "check" followed by an "act" — and an attacker can submit concurrent requests to exploit the small timing window. These flaws often lead to bypassing rate limits, duplicate transactions, or inconsistent state updates that enable privilege escalation or data corruption.

I have completed many race-condition labs on the Web Security Academy. The two documented below were selected because they illustrate different classes of race issues and showcase the diversity of practical impacts that race conditions can have.

---

## Featured Labs

### 1. Bypassing Rate Limits via Race Conditions

* **Objective:** Exploit non-atomic rate-limit or one-shot checks by sending concurrent requests to bypass protections.
* **Key technique:** Use concurrent requests (Burp Intruder / Turbo Intruder or custom scripts) to race check-and-act sequences so multiple requests succeed before state updates.
* **Lab file:** `lab-01-bypass-rate-limits-via-race-conditions.md`

### 2. Single-endpoint Race Conditions (pending-state collision)

* **Objective:** Cause collisions in a single pending-state entry (e.g., pending email/token) by issuing parallel updates so confirmation tokens are mismatched and can be used to hijack accounts.
* **Key technique:** Send parallel email-change requests and exploit timing differences between database updates and asynchronous email-sending tasks.
* **Lab file:** `lab-03-single-endpoint-race-conditions.md`

---

## Lessons and insights

* Race conditions can be subtle but high-impact; they often require tooling to reproduce (parallel requests, Turbo Intruder).
* Different race patterns produce different outcomes: duplicated actions (financial risk), bypassed rate-limits (abuse), and data/identity collisions (account takeover).
* Mitigation strategies generally require making operations atomic (DB transactions, locks), using idempotency keys, binding tokens to specific records, and adding orthogonal protections (global throttles, anomaly detection).

---




---


