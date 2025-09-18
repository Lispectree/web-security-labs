# Lab 01 — Exploiting NoSQL Operator Injection to Bypass Authentication (featured)

**Vulnerability:** NoSQL Injection — operator injection in query parameters allowing authentication bypass
**Lab source:** PortSwigger Web Security Academy


---

## Short overview

This lab demonstrates how improperly-sanitized input may allow injection of NoSQL operators (such as MongoDB operators) into authentication queries. By supplying JSON-style operator payloads for username and password parameters, an attacker can manipulate the server-side query to bypass authentication or select unexpected user records (including administrative accounts).

## Goal

* Determine whether authentication parameters accept NoSQL operator payloads.
* Use operator injection to bypass authentication and obtain access as an administrative user in the controlled lab environment.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)

I logged into the application with the supplied credentials `wiener:peter` using Burp's browser so that Burp Suite captured the requests. In Proxy → HTTP history I found the `POST /login` request and sent it to Burp Repeater for experimentation. In Repeater I modified the `username` parameter to a MongoDB operator expression `{"$ne":""}` and resent the request; this caused the server to accept the login despite not providing a valid username, indicating the backend interpreted the input as a NoSQL operator. I replaced the username value with a regex operator and observed that the login also succeeded, confirming support for operator expressions. I then set the username back  and changed the `password` parameter to `{"$ne":""}`; resending the request produced a response suggesting an unexpected number of records were returned, implying multiple users matched the query. With the password set to `{"$ne":""}`, I changed the username to `{"$regex":"admin.*"}` and resent the request — the server authenticated me as the administrator. I used the Repeater response’s "Show response in browser" feature to open the authenticated session in Burp's browser, copied the URL, and used it to access the application as the administrator. 

## What I observed (sanitized)

* The authentication endpoint accepted JSON-like operator payloads and interpreted them as NoSQL operators rather than literal strings.
* Injecting `{"$ne":""}` or `{"$regex":"..."}` into username/password parameters allowed bypassing normal credential checks.
* Using operator injection against both username and password enabled selection of the administrative account and full authentication as admin.

---

## Proof (sanitized)

```
POST /login HTTP/1.1
Host: vulnerable-app.local
Content-Type: application/x-www-form-urlencoded

username={"$ne":""}&password=irrelevant
```

```
POST /login HTTP/1.1
Host: vulnerable-app.local
Content-Type: application/x-www-form-urlencoded

username={"$regex":"admin.*"}&password={"$ne":""}
```

---

## Mitigation

* Treat input as data, not query syntax: do not allow JSON or operator syntax to be injected into NoSQL queries.
* Use parameterized queries or driver-level APIs that separate query structure from data and do not interpret user input as operators.
* Validate and sanitize input server-side — explicitly allow expected formats for username and password fields (e.g., alphanumeric, length limits).
* Apply account lockout, multifactor authentication, and monitoring to detect anomalous login behavior.

---

## Lessons learned

* NoSQL injection can be achieved by supplying operator-like payloads where user input is incorporated into query structures.
* Authentication endpoints are high-value targets for injection — ensure strict input validation and use safe APIs to build database queries.
* Defensive measures should include both safe query construction and runtime protections (rate limiting, anomalies, MFA).
