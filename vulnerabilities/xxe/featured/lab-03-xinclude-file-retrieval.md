# Lab 03 — Exploiting XInclude to retrieve files (featured)

**Vulnerability:** XML External Inclusion (XInclude) — file retrieval via XInclude processing
**Lab source:** PortSwigger Web Security Academy


---

## Short overview

This lab demonstrates how XML XInclude processing can be abused to retrieve local files from the server. By crafting an XML payload that uses the XInclude namespace and references a local file (for example `/etc/passwd`) with `parse="text"`, an attacker can cause the server to include the file contents in the XML processing result and leak sensitive files in the application response.

## Goal

* Verify whether the application's XML processing supports XInclude and whether included files are resolved and returned.
* Use an XInclude payload to retrieve local files (e.g., `/etc/passwd`) in the controlled lab environment.


---

## My steps (narrative, exactly what I did)

I visited a product page and clicked the "Check stock" feature while intercepting requests in Burp Suite. I captured the POST request containing the XML payload and sent it to Burp Repeater. I replaced the `productId` value with an XInclude payload that requests inclusion of a local file as text.

``

I resent the modified request. The server processed the XInclude directive and the response contained the contents of `/etc/passwd`.

---

## What I observed (sanitized)

* The application’s XML processor accepted XInclude directives and resolved the referenced local file.
* The response included the text contents of the referenced file, demonstrating that XInclude processing can be abused to retrieve local files.
* XInclude-based retrieval is another vector similar to XXE and should be mitigated alongside other XML external resource features.

---

## Proof (sanitized)

* The modified POST request sent via Burp Repeater containing the XInclude payload (sanitized).
* The server response showing the contents of `/etc/passwd` (sensitive parts redacted).


---

## Mitigation

* Disable XInclude processing if it is not required by the application or configure the parser to refuse fetching external resources.
* Use secure XML parsing libraries that disallow external resource resolution by default.
* Apply network-level restrictions that prevent the application from reading local sensitive files or fetching internal resources.
* Validate and sanitize XML inputs and perform security testing for all XML features (DTDs, entities, XInclude).

---

## Lessons learned

* XInclude is an XML feature that can introduce the same risks as XXE if the parser resolves external resources.
* Secure XML handling requires disabling all unnecessary external-resource features and constraining parser behavior.
* Defense-in-depth (parser configuration, network restrictions, least privilege) reduces the impact of accidental or malicious XML inclusion.
