# Lab 07 — Blind SQL injection with time delays (featured)

**Vulnerability:** SQL Injection — Time-based blind (inference)  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
Time-based blind SQLi infers backend true/false conditions by observing measurable response delays when the application does not return query results. This technique is useful when error messages and direct output are unavailable.

## Goal
Demonstrate safe, non-destructive timing probes that show the application exhibits predictable delays under certain inputs, enabling inference of backend logic without extracting data.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + HTTP proxy (Burp Suite) to intercept requests and measure response timing; simple timing script or repeated manual requests for confirmation.

---

## Step-by-step (sanitized, lab-only)

1. **Capture the target request.** Intercept the HTTP request carrying the parameter to test (e.g., category or id). Confirm the parameter is included in server-side logic.  
2. **Establish baseline timing.** Send several baseline requests with normal inputs and record average/median response times to understand normal noise.  
3. **Send timing probes.** Modify the intercepted request to include a probe that causes a measurable delay if a backend condition is true. Forward the modified request and measure response time.  
   - Use repeated requests and statistical checks (median or trimmed mean) to separate signal from noise.  
4. **Validate repeatability.** Repeat the probe multiple times and compare timing distributions between baseline and probe cases to ensure the delay is consistent and not due to network jitter.  
5. **Iterate to infer data.** Use conditional probes to test specific true/false conditions (lab-only). Accumulate observations and infer backend behavior where appropriate.

> **Sanitization note:** Do not publish raw delay-exploiting payloads in public repos. Keep exact probe strings in private notes.

---

## What I observed (sanitized)
- Baseline median response time: ~T_base ms (replace with your measured value).  
- Probe median response time: ~T_probe ms (replace with your measured value), showing a consistent and statistically significant delay across repeated requests.  
- The timing difference was reproducible, indicating the server evaluated the injected condition on the backend.

Add sanitized proof image: `images/lab-07-timing.png` — 

---

## Variations tried
- Increased repetition count to improve confidence in the timing signal.  
- Adjusted probe spacing and rate-limiting to avoid creating noisy or throttled measurements.  
- Combined timing checks with boolean probes where appropriate to corroborate findings.

---

## Mitigation
- Remove timing side-channels where feasible (standardize response times, avoid lengthy conditional operations dependent on untrusted input).  
- Use parameterized queries / prepared statements and validate inputs.  
- Implement rate-limiting, anomaly detection, and logging to detect repeated timing probes.  
- Restrict DB functions that enable long server-side sleeps or delays where possible.

---

## Lessons learned
- Timing-based inference is reliable when repeated and analyzed statistically; single-request measurements are prone to false positives.  
- For reporting, emphasize measurement methodology and sanitized evidence rather than exact probe payloads.


