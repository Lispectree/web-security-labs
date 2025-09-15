 Lab 04 — SQL injection: querying DB type and version (MySQL / Microsoft) (summary)

**Type:** In-band SQL Injection — UNION-based reconnaissance / DB fingerprinting

**Goal:** Determine the number of columns returned by the original query, identify which returned column(s) display text, and use a UNION-style probe to display the database version string (lab-only).

**Environment & tools:** PortSwigger Web Security Academy (lab); Browser + HTTP proxy (Burp Suite) to intercept and edit requests and inspect responses.

**What I did (sanitized):**
1. Intercepted the HTTP request that sets the product category filter with Burp Suite to capture the parameter used by the backend query.  
2. Determined the original query’s column count by sending `UNION` probes with neutral placeholders (e.g., repeated `NULL`s) and observing when the server accepted a syntactically compatible result shape.  
3. Located which column positions accept/display text by replacing placeholders one at a time with harmless marker strings and inspecting the rendered response; this showed that the query returns **two** columns and both columns reflect text.  
4. Using the discovered column mapping, sent a controlled `UNION` probe that placed the database version value into a text-accepting column (lab-only) and verified the DB version string appeared in the application response.

**What I observed:**  
- The `UNION` enumeration revealed the query returns **2 columns**.  
- Marker strings confirmed that **both columns** can accept/display text.  
- A subsequent `UNION` probe caused the application to display the DB version string in the response, confirming the DBMS version information could be read in this lab environment.

**Mitigation:**  
- Use parameterized queries / prepared statements; never concatenate untrusted input into SQL statements.  
- Suppress detailed DB error output and avoid returning raw DB results to end users.  
- Restrict access to DB metadata and system views via least privilege.  
- Monitor and rate-limit suspicious `UNION`-style probes and unusual query patterns.



