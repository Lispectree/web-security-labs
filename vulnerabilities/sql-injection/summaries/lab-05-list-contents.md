# Lab 05 — SQL injection: listing database contents (non-Oracle) (summary)

**Type:** In-band SQL Injection — Data enumeration and credential discovery 

**Goal:** Determine whether the application can be made to reveal database schema and table contents, identify a credentials table and its username/password columns, and verify an administrator account password (all performed safely in the lab).

**Environment & tools:** PortSwigger Web Security Academy (lab); Browser + HTTP proxy (Burp Suite) to intercept and modify requests and inspect responses.

**What I did (sanitized):**
1. Intercepted the request that sets the product category filter and confirmed the parameter was used server-side in a SQL query.  
2. Enumerated the original query’s column count using non-destructive `UNION` probes (neutral placeholders) until the response accepted a compatible column shape.  
3. Identified which returned column positions display text by replacing placeholders one-at-a-time with harmless marker strings and observing where those markers appeared in responses — this showed the query returned two columns and both could display text.  
4. Performed controlled, read-only `UNION`-style enumeration against the database metadata view (lab environment) to list table names and located the table that stores user credentials.  
5. Requested the column listing for that credentials table (lab-only) to discover which columns hold usernames and password values.  
6. Retrieved username/password rows for the credentials table in the lab environment, identified the administrator entry, and used that credential to verify authentication in the lab.

**What I observed:**  
- The application accepted `UNION`-style probes when the placeholder count matched the original query, enabling safe enumeration.  
- The lab showed a credentials table and plaintext-or-weakly-protected password values for users (sanitized); the administrator account’s password matched the retrieved value and allowed login in the controlled lab environment.

**Mitigation:**  
- Use parameterized queries / prepared statements and avoid building SQL from untrusted input.  
- Enforce least-privilege for database accounts; do not allow web-facing accounts to read schema/metadata or arbitrary tables.  
- Store passwords securely using strong hashing (bcrypt/argon2) with per-user salts — never store plaintext or reversible encryption for passwords.  
- Suppress detailed database errors and audit/log access to sensitive tables; monitor for repeated enumeration attempts and rate-limit suspicious patterns.  
- Use defense-in-depth: input validation, output encoding, WAF tuned rules, and strong authentication controls (MFA, account lockout).


