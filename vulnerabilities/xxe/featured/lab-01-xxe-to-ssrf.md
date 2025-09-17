# Lab 01 — Exploiting XXE to perform SSRF attacks (featured)

**Vulnerability:** XML External Entity (XXE) — SSRF via XML external entities
**Lab source:** PortSwigger Web Security Academy

---

## Short overview

This lab demonstrates how an XML External Entity (XXE) vulnerability can be used to make the server perform server-side requests to internal resources (SSRF). By injecting a crafted DTD that defines an external entity pointing at internal endpoints, an attacker can cause the application to include the external resource contents in the XML processing result and exfiltrate sensitive data such as metadata or credentials.

## Goal

* Confirm whether the XML parser accepts external entities and whether those entities are resolved by the server.
* Use an XXE payload to make the server fetch internal metadata endpoints and extract sensitive information (for example, cloud instance metadata or credentials) in the controlled lab environment.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)

I navigated to a product page and clicked the "Check stock" feature while intercepting requests in Burp Suite. I captured the POST request that contained the XML payload and sent it to Burp Repeater. Between the XML declaration and the `stockCheck` element I inserted a DOCTYPE declaration that defined an external entity referencing an internal metadata endpoint, for example: `<!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>`. I then replaced the `productId` value with a reference to the external entity (`&xxe;`) and resent the request. The server responded with an error message containing the returned data prefixed by "Invalid product ID:", and the included content showed the response from the metadata service. Iteratively, I updated the SYSTEM URL in the DTD to point deeper into the metadata API (for example `/latest/meta-data/iam/security-credentials/admin`) and resent the request. Eventually the response contained JSON including a `SecretAccessKey`, which I used as the proof artifact. I sanitized any sensitive values before saving artifacts.

---

## What I observed (sanitized)

* The XML parser resolved external entities and the server fetched the specified internal metadata endpoints.
* Responses from the internal metadata service were included in the application's error output (the server returned "Invalid product ID:" followed by metadata content).
* Iterating the DTD target allowed discovery of deeper metadata paths until a JSON blob containing credential-like fields (e.g., `SecretAccessKey`) was returned.

---

## Proof (sanitized)

* The HTTP request sent via Burp Repeater containing the DTD and `&xxe;` reference (sanitized).
* The server response showing `Invalid product ID:` followed by the fetched metadata output (sensitive fields redacted).



## Mitigation

* Disable external entity resolution in XML parsers (turn off DTDs or external entity processing) or use parser configurations that do not resolve external entities by default.
* Validate and canonicalize XML input and use safe parsing libraries; prefer data formats that avoid entity processing when possible (e.g., JSON).
* Implement network-level controls that prevent application servers from reaching internal metadata services (e.g., block access to 169.254.169.254).
* Apply principle of least privilege to service roles so that even if metadata is retrieved, exposed credentials have minimal privileges and are rotated regularly.

---

## Lessons learned

* XXE can be leveraged as an SSRF vector to reach internal resources and extract sensitive information.
* Disabling DTD/external entity resolution and restricting outbound network access are effective mitigations.
* Parsing libraries should be configured securely and tested for XXE during development and security reviews.
