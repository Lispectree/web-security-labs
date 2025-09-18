# XXE (XML External Entity) — Featured Labs

## Overview

XML External Entity (XXE) and related XML features (such as XInclude) can allow attackers to make the application read local files, perform SSRF to internal endpoints, or otherwise access sensitive resources when the XML parser resolves external entities or includes. The three labs documented here demonstrate different XXE/XInclude techniques and their real impact.

I have completed many more labs on XXE and XML-related vulnerabilities at the Web Security Academy. The three selected here were chosen because they showcase diverse exploitation techniques and consequences: SSRF via XXE, blind error-based exfiltration, and XInclude-based file retrieval.

---

## Featured Labs

### 1. Exploiting XXE to perform SSRF attacks

* **Objective:** Use an external entity to make the server fetch internal metadata endpoints (SSRF via XXE).
* **Key technique:** Inject a DOCTYPE and external entity that references internal IPs (e.g., the instance metadata service) and include the entity in the XML payload.
* **Lab file:** `lab-01-xxe-to-ssrf.md`

### 2. Exploiting blind XXE to retrieve data via error messages

* **Objective:** Use an externally hosted malicious DTD to force the parser to read local files and cause an error that contains file contents.
* **Key technique:** Host a crafted DTD that reads a local file via a file:// entity and references it in a way that triggers an error-based leak when imported.
* **Lab file:** `lab-02-blind-xxe-error-retrieval.md`

### 3. Exploiting XInclude to retrieve files

* **Objective:** Retrieve local files by using XInclude directives in XML payloads.
* **Key technique:** Inject an XInclude payload (`<xi:include parse="text" href="file:///..."/>`) and observe the parser including the referenced file content.
* **Lab file:** `lab-03-xinclude-file-retrieval.md`

---

## Lessons and insights

* XXE and related XML features are powerful and dangerous when parsers resolve external resources.
* Blind techniques (error-based or indirect) are especially valuable when direct entity expansion isn't returned in normal responses.
* Mitigations include disabling DTDs and external entity resolution, disabling XInclude, using safe parsers, and applying network-level controls that block access to sensitive internal services.

---


---


---

