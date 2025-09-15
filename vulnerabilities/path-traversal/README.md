# Path Traversal

**Overview**  
Path Traversal (also known as directory traversal) is a web vulnerability that allows attackers to access files and directories that are stored outside the intended directory. By manipulating user input, an attacker can read sensitive system files, configuration files, or other protected resources.  

In this section, I focused on three featured labs from the PortSwigger Web Security Academy that demonstrate different Path Traversal techniques.

---

## Featured Labs

### 1. Absolute Path Bypass
**Objective:** Retrieve system files even when traversal sequences are blocked.  
**Key technique:** Using absolute paths to bypass naive traversal filters.  
**Lab file:** [`lab-01-absolute-path-bypass.md`](./featured/lab-01-absolute-path-bypass.md)

### 2. Double-Encoded Traversal Bypass
**Objective:** Access sensitive files despite filtering of single traversal sequences.  
**Key technique:** Using double-encoded traversal sequences (`..%252f`) to bypass server-side input validation.  
**Lab file:** [`lab-02-double-encoded-bypass.md`](./featured/lab-02-double-encoded-bypass.md)

### 3. Null Byte Bypass
**Objective:** Bypass file extension validation to retrieve restricted files.  
**Key technique:** Injecting a null byte (`%00`) to terminate the filename early and bypass extension checks.  
**Lab file:** [`lab-03-null-byte-bypass.md`](./featured/lab-03-null-byte-bypass.md)

---

## Lessons learned
- Proper path validation and canonicalization are essential to prevent traversal attacks.  
- Input encoding (double-encoding, null bytes) can bypass naive filters.  
- Restricting file access to safe directories and validating paths server-side is critical.  
- Automated tools like Burp Suite make it easy to safely test and understand these vulnerabilities.

---

## Tools used
- Browser  
- Burp Suite (Proxy, Repeater, Intruder)

