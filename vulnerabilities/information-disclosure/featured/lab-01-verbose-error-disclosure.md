# Information Disclosure — Featured Examples

**Vulnerability:** Information Disclosure — verbose errors & exposed files
**Lab source:** PortSwigger Web Security Academy (examples)


---

## Short overview

Information disclosure happens when an application reveals sensitive implementation or configuration details to an attacker — for example via verbose error messages, stack traces, or exposed backup/configuration files. These leaks often don't themselves give full control, but they greatly increase reconnaissance effectiveness and can be chained into higher-impact attacks.


## Goal

* Trigger and capture verbose server errors to extract implementation details (framework, version, file paths).
* Discover and retrieve exposed backup or configuration files that may contain secrets or internal configuration.
* Explain how these disclosures increase attacker options and outline mitigations.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled labs)
* Tools: Browser + Burp Suite (Proxy, HTTP history, Repeater)
* Optional: content discovery tools (dirb / ffuf / feroxbuster), curl

---

## My steps (narrative, exactly what I did)

I navigated to a product page while Burp Suite proxy was running and captured the `GET /product?productId=1` request in Proxy → HTTP history. I sent that request to Burp Repeater and modified the `productId` parameter to a non-integer value (for example `\"example\"`) and resent the request; the server returned a 500-level error containing a full stack trace that included `Apache Struts 2.3.31`, which I used to solve the lab. Separately, I ran a content-discovery scan against the application using a common wordlist and inspected directory hits in Burp/scan output; I found a publicly accessible backup/config file (for example `backup.zip` or `config.php.bak`) and downloaded it, then inspected the contents for sensitive information such as API keys, database connection strings, or internal hostnames. I sanitized any secrets before saving artifacts.

---

## What I observed (sanitized)

* Sending unexpected input types caused the application to throw a server-side exception that was returned to the client as a full stack trace, leaking framework and version information (`Apache Struts 2.3.31`).
* Content discovery revealed one or more backup/configuration files left on the server that were directly downloadable without authentication. These files contained sensitive configuration details (example: DB hostnames, partial credentials, or internal paths) that could be used for further exploitation.

---


---

## Mitigation

* Disable verbose error output in production. Return generic, user-friendly error pages while logging full error details server-side for diagnostics.
* Implement centralized error handling that prevents stack traces and implementation details from being sent to end users.
* Remove backup and temporary files from webroot and restrict file access using proper filesystem permissions.
* Harden deployment processes: avoid committing credentials or backups to web-accessible locations; use secure storage for secrets and rotate credentials found in backups.
* Use runtime protections and dependency scanning to detect and patch known vulnerable framework versions; maintain an inventory of dependencies and apply security updates promptly.

---

## Lessons learned

* Small information leaks (verbose errors, forgotten backups) dramatically lower the cost of reconnaissance and can speed up exploitation.
* Blind or seemingly low-impact disclosures are often the missing piece that turns a vague probe into a reliable exploit path.
* Prevention is largely operational: safe error handling, disciplined deployment, and minimizing attack surface (no backup files in webroot) go a long way.

---

