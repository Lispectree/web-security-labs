# Lab 06 — SQL injection UNION attack: determine column count & find text column (featured)

**Vulnerability:** SQL Injection — UNION-style (in-band)  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how to determine the number of columns returned by a query and which of those columns accept/display text, using non-destructive UNION probes in a controlled lab environment. Identifying column count and text-capable columns is a standard precursor to safe in-band data discovery.

## Goal
1. Determine the original query's column count so a `UNION SELECT` can be crafted with a compatible shape.  
2. Find which column position(s) reflect text so subsequent probes can place readable values there.

## Environment & tools
- PortSwigger Web Security Academy (lab) — controlled environment only.  
- Tools: Browser + HTTP proxy (Burp Suite) to intercept and modify requests and to inspect responses.

---

## Step-by-step (sanitized, lab-only)

### 1) Capture the request
1. Intercept the request that sets the product category filter with your proxy.  
2. Identify the parameter used by the backend query (the one you will test).

### 2) Find the number of columns (UNION column-count)
1. Send `UNION` probes with an increasing number of neutral placeholders (e.g., `NULL`) to match the original query's column count.  
   - Example structure: `... UNION SELECT NULL, NULL, NULL ...` — increase the number of `NULL`s until the server accepts the `UNION` (page renders without a column-count or syntax error).  
2. When the server stops returning a column-count error and the page renders, you have matched the correct number of columns (the column count is the number of placeholders that worked).

### 3) Find which columns accept/display text
1. With the correct column count known (for this lab it was **3 columns**), replace one `NULL` at a time with a harmless marker string (a short label) while keeping the other positions as `NULL`.  
   - Example structure: `... UNION SELECT 'MARKER', NULL, NULL ...`  
2. Inspect the rendered response for the marker string to identify which column position is reflected as text.  
3. If a marker causes an error, move to the next column position and try again. Repeat until you map the text-capable column(s).

> In this lab you verified the query returned **three** columns, then replaced each `NULL` with a marker to find the text-accepting position(s).

### 4) Verify & capture evidence
1. Once you know which column(s) accept text, use a harmless marker in that column and confirm the marker appears in the page response.  
2. Capture a sanitized screenshot showing the marker displayed in the application (this is your proof).  
3. Repeat the test to ensure the behavior is consistent.

---

## What I observed (sanitized)
- The application required **3** columns in the `UNION SELECT` to avoid column-count errors.  
- By replacing `NULL`s one at a time with a harmless marker string I discovered which column position(s) reflected text in the response.  
- The marker appeared consistently in the same column position across multiple requests, confirming the mapping.

 `images/lab-06-proof.png` — 
---

## Variations tried
- Tried markers in different positions and confirmed only the identified column(s) displayed text.  
- Verified results across repeated requests to rule out transient behavior.

---

## Mitigation
- Use parameterized queries / prepared statements — never build SQL by concatenating untrusted input.  
- Suppress verbose DB error output and avoid returning raw DB output to users.  
- Restrict DB privileges and limit which columns/tables are exposed via web queries.  
- Monitor for repeated UNION-style probes and implement rate-limiting or alerts.

---

## Lessons learned
- The column-count → marker mapping sequence is a reliable, low-noise method to safely determine where readable content can be reflected in an application.  
- Documenting the **observations** (how the page responded) is more useful and safer to publish than raw payloads—keep exact exploit strings in private notes.

