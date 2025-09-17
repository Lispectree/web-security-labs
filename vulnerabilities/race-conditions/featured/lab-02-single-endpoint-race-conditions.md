# Lab 02 — Single-endpoint Race Conditions 

**Vulnerability:** Race Condition — non-atomic updates to a single pending state leading to cross-request collisions
**Lab source:** PortSwigger Web Security Academy

---

## Short overview

This lab demonstrates how a single database field used to store a pending state (for example, a pending email address and its confirmation token) can be subject to race conditions. When multiple requests concurrently update that single pending entry, confirmation emails can be sent containing mismatched data — enabling an attacker to cause a confirmation link intended for their address to instead confirm another user's email and gain elevated access.

## Goal

* Determine whether the application stores only one pending email address/token per account and whether that pending state can be raced.
* Exploit the race by issuing concurrent email-change requests so that a confirmation link intended for the attacker's address contains another user's address, allowing the attacker to hijack that user's account in the controlled lab environment.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)

I logged in and attempted to change my account email to a test address under my exploit server domain to observe the normal flow: the application sends a confirmation email containing a unique token which must be clicked to confirm the change. After confirming once to see the expected behaviour, I inferred that the application stores a single pending email entry because submitting a second new address invalidated the first confirmation link.

To benchmark the behaviour I captured the POST `/my-account/change-email` request in Burp Proxy and sent it to Repeater. I grouped the request and duplicated the tab multiple times (I created 19 duplicates), modifying each tab so the local-part of the email was unique  Sending these requests in sequence resulted in a single confirmation email per submitted address as expected. Next, I sent the same group of requests in parallel. After the parallel burst I checked the exploit inbox and noticed that some confirmation emails contained a recipient address that did not match the email value in the email body — indicating a collision between the pending database update and the email-rendering/sending task.

To prove the concept, I created a two-request group in Repeater where one request set the email to my exploit inbox (`anything@exploit-<ID>...`) and the other set the email to `carlos@ginandjuice.shop`. I sent both requests in parallel. When the confirmation emails arrived, one email contained `carlos@ginandjuice.shop` in its body but the link pointed to the token generated for my session. Clicking that link confirmed Carlos's email change. After the confirmation, my account showed the admin panel link; I visited the admin UI and deleted the user `carlos` to solve the lab. I sanitized all saved artifacts by redacting tokens, real email addresses, and session identifiers.

---

## What I observed (sanitized)

* The application stores a single pending email/token entry per account and updates it on each email-change request rather than appending multiple pending entries.
* When multiple requests updated this single pending entry concurrently, confirmation emails were sometimes rendered/sent using stale or mismatched data, resulting in tokens that confirmed the wrong target address.
* Exploiting this collision allowed confirmation of another user's email and subsequent privilege access in the controlled lab.

---

## Proof

* Captured POST `/my-account/change-email` request used in Repeater to issue parallel updates.
* One of the received confirmation emails contained `carlos@ginandjuice.shop` in its body while the confirmation token corresponded to my session (sanitized).
* Admin UI screenshot showing access after the email confirmation (sanitized).


---

## Mitigation

* Do not store transient pending operations in a single, replaceable database field without concurrency controls; maintain per-request pending records or use atomic update semantics that include creator/session binding.
* Generate confirmation tokens that are cryptographically bound to both the intended email address and the session/user id, and verify both values at confirmation time.
* Use transactional operations to create and persist pending confirmation records before any asynchronous email-sending tasks read them.
* Consider implementing per-request identifiers and use queued background jobs that fetch the exact record to email, not the current value of a mutable single field.
* Log and monitor for concurrent pending-state changes and anomalous confirmation flows.

---

## Lessons learned

* Single-field pending-state designs are vulnerable to race conditions when updates and asynchronous tasks are not tightly coupled.
* Parallel requests can create subtle collisions that lead to security-critical mismatches between tokens and intended targets.
* Robust designs use immutable pending records, strong token binding, and atomic creation patterns to eliminate the race window.
