# Lab 01 — Bypassing Login Rate Limits via Race Condition (featured)

**Vulnerability:** Race Condition — non-atomic login attempt counters enabling credential stuffing / lockout bypass  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab shows how a per-username rate limit (failed-login counter) that is updated non-atomically can be defeated by racing concurrent login attempts. By queuing many login requests simultaneously you can submit more failed attempts than the server's threshold before the counter is updated, and by targeting another username you can discover a valid password via parallelized guessing and bypass the lockout protections.

## Goal
- Determine whether failed-login counters or lockout mechanisms are updated atomically.  
- Exploit a race condition to submit multiple login attempts in parallel and bypass per-username rate limits, then use the technique to identify a working password and authenticate as the target user in the controlled lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history, Turbo Intruder), optional Python scripting for concurrent requests

---

## My steps (narrative, exactly what I did)
I experimented with the login flow while Burp Suite proxy was running. I deliberately submitted incorrect passwords for my account until I observed that more than three incorrect attempts resulted in a temporary lockout for that username, while other usernames continued to show the normal "Invalid username or password" response — indicating the limit is enforced per-username and stored server-side. To probe for a race window I captured a POST /login request that produced an unsuccessful login and sent it to Burp Repeater. I created a tab group and duplicated that tab many times (I created 19 duplicates) to simulate many attempts. Sending the group sequentially confirmed the expected lockout after a few additional failed attempts. I then sent the group in parallel and observed that several requests returned the normal invalid-login response even though the account lock was ultimately triggered — showing I could submit multiple attempts inside the race window.

To prove the concept against the target account, I used Turbo Intruder. From a captured POST /login request I highlighted the password value and used Extensions → Turbo Intruder → Send to turbo intruder. In the Turbo Intruder editor I set the password field as the payload position and selected the `examples/race-single-packet-attack.py` template. I copied a list of candidate passwords to my clipboard and configured the script to queue a request per password using a single-packet gate so all requests would be released at once (`engine.openGate('1')`). I changed the username parameter to `carlos`, launched the attack, and studied the responses. When a 302 redirect appeared for one of the payloads it indicated a successful login; I noted the corresponding password from the payload column. After waiting for the account lock to reset, I logged in as `carlos` using the identified password, accessed the admin panel, and deleted the user `carlos` to solve the lab. All saved artifacts were sanitized to remove session identifiers and any PII.

---

## What I observed (sanitized)
- Failed-login counters and lockout enforcement were implemented per-username and stored server-side.  
- Sequential attempts triggered the lockout as expected, but parallel/near-simultaneous requests allowed more than the threshold number of attempts to be processed as "invalid" before the server updated the counter, demonstrating a race window.  
- Using Turbo Intruder to send many candidate-password requests in a single gated burst allowed me to discover a valid password without being reliably blocked by the lockout mechanism.

---

## Proof
- Burp Repeater/Turbo Intruder request log showing multiple concurrent POST /login requests with identical parameters sent in parallel.  
- Response evidence where multiple requests returned the normal invalid-login message while the account became locked overall.  
- A Turbo Intruder response entry with HTTP 302 indicating a successful login; the payload column identifies the working password (redacted in saved artifacts).  
- Screenshot of the admin UI after logging in as the target and performing the required action 


## Mitigation
- Make authentication-related operations atomic: perform increment-and-check in a single transactional operation so concurrent attempts cannot both pass the pre-check. Use database transactions (`SELECT ... FOR UPDATE`) or atomic counter primitives.  
- Use strong, server-side idempotency/locking mechanisms for one-shot actions and for state that controls lockout.  
- Implement additional, orthogonal protections: global rate limits per IP or session, progressive delays on failed attempts, CAPTCHA after repeated failures, and anomaly detection for bursts of concurrent attempts.  
- Record and alert on high-concurrency login attempts and unusual patterns from single clients.

---

## Lessons learned
- Race conditions in authentication logic are subtle: a seemingly robust per-username counter can be bypassed via parallel requests if updates are not atomic.  
- Reproducing race conditions requires precise tooling (parallel requests, Turbo Intruder) rather than simple sequential testing.  
- Defenses should not rely on eventual consistency for security-critical counters — atomic operations and multiple layers of throttling are essential.
