# Lab 03 — SQL injection: querying DB type & version (Oracle) (summary)

**Type:** In-band SQL Injection — UNION-based reconnaissance / DB fingerprinting

**Goal:** Safely determine the number of columns returned by the original query, identify which column(s) accept/display text, and use a UNION-style probe to surface the Oracle database version (from `v$version`) in the lab environment.

**Environment & tools:** PortSwigger Web Security Academy (lab); Browser + HTTP proxy (Burp Suite) to intercept and modify requests and inspect responses.

**What I did (sanitized):**
1. Intercepted the target request and confirmed which parameter influenced the backend query.
2. Determined the original query's column count by submitting UNION probes with neutral placeholders until the server accepted a compatible column shape.
3. Located which returned column(s) reflect text by replacing placeholders one-at-a-time with harmless marker strings and observing where markers appear in the rendered response.
4. After identifying a text-accepting column, crafted a controlled UNION probe that placed the Oracle `BANNER` (version) into that visible column and verified the DB version appeared in the page output.

**What I observed:**  
By matching the column count and mapping the text-reflecting column, the UNION probe displayed the database `BANNER` (version) in the application response — confirming Oracle as the DB engine in this lab. Results were reproduced across multiple requests and captured as sanitized evidence.

**Mitigation:**  
- Use parameterized queries / prepared statements and avoid constructing SQL from untrusted input.  
- Suppress or genericize database error messages; log details server-side only.  
- Limit access to DB metadata (e.g., `v$version`) through least-privilege accounts; monitor for repeated UNION-style probes.



