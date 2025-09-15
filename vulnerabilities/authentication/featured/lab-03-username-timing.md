# Lab 03 — Username Enumeration via Response Timing (featured)

**Vulnerability:** Authentication — Username Enumeration via Timing Side-Channel  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored how subtle differences in server response times could reveal valid usernames, even when error messages were uniform. I used timing as a side-channel to identify a valid account and eventually determine the correct password.

## Goal
- Identify valid usernames using response timing.  
- Bypass rate-limiting protections and confirm account credentials in the lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater, Intruder)

---

## My steps (narrative, sanitized)

1. I started by submitting an invalid username and password on the login page while Burp Suite was running. I sent the POST `/login` request to Repeater to inspect the responses. I noticed that too many invalid attempts would trigger an IP block in the lab.

2. I saw that the `X-Forwarded-For` header was supported, which allowed me to spoof my IP and bypass the rate-limit. I experimented with several usernames and passwords and observed the response times. When I submitted an invalid username, the response times were fairly consistent. However, when I used my own username, the response time increased proportionally to the password length.

3. To automate testing, I sent the request to Burp Intruder. I selected a **Pitchfork attack**, added payload positions for the `X-Forwarded-For` header and the username, and set the password to a long string to exaggerate timing differences. I configured the payloads: a numeric range for IP spoofing and a list of candidate usernames.

4. When the attack finished, I checked the results and noticed one username consistently had a longer response time. I repeated the test a few times to confirm consistency and noted this username as valid.

5. Next, I set up a new Intruder attack using the identified username. I added payload positions for the `X-Forwarded-For` header and the password parameter. I used the numeric range for IP spoofing and a list of candidate passwords. Once the attack completed, I found the password that produced a 302 status, indicating a successful login.

6. Finally, I logged in using the discovered username and password, accessed the account page, and confirmed the lab was solved.

---

## My observations (sanitized)
- Using the `X-Forwarded-For` header effectively bypassed IP-based rate limiting.  
- Timing differences revealed a valid username reliably.  
- The timing-based side-channel allowed me to identify both a valid username and its password.  

`images/lab-03-username-timing.png` 

---

## Mitigation
- Ensure consistent response times for all login attempts.  
- Enforce robust rate-limiting that cannot be bypassed via spoofed headers.  
- Return generic error messages to avoid disclosing account existence.  
- Monitor for repeated login attempts and unusual patterns.

---

## Lessons learned
- Even tiny timing variations can leak sensitive information.  
- Automating attacks with Burp Intruder allows safe, controlled testing of timing side-channels.  
- Designing authentication flows to prevent information leakage requires attention to both content and timing of responses.
