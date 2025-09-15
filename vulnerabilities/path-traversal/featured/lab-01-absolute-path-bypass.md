# Lab 01 — File Path Traversal: Absolute Path Bypass (featured)

**Vulnerability:** Path Traversal — Absolute Path Access  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored how poorly validated file path parameters can allow access to sensitive system files. Even when traversal sequences are blocked, absolute paths can bypass restrictions, exposing critical data.

## Goal
- Retrieve system files that should be inaccessible through the web application.  
- Understand how blocking traversal sequences alone is insufficient for secure file access.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I began by observing how the web application fetched product images. Using Burp Suite, I intercepted a request for an image and focused on the `filename` parameter.  

To test the file access controls, I replaced the normal filename with an absolute path: `/etc/passwd`. I sent the modified request and examined the response in Burp. The server returned the contents of `/etc/passwd`, confirming that absolute paths bypassed the input validation designed to block traversal sequences.  

This approach demonstrated that simply filtering traversal characters is not enough to prevent unauthorized file access.

---

## My observations (sanitized)
- The server allowed retrieval of sensitive files when given an absolute path.  
- Input validation on traversal sequences alone did not secure the file access functionality.  
- The attack successfully revealed the system’s user account file in the lab environment.

: `images/lab-01-absolute-path-bypass.png`

---

## Mitigation
- Normalize and validate all file paths on the server side.  
- Avoid accepting user-supplied paths for file retrieval.  
- Restrict file access to a safe, predefined directory.  
- Implement whitelist validation for allowed filenames.

---

## Lessons learned
- Blocking traversal sequences alone is insufficient to secure file access.  
- Absolute paths can bypass naive input filtering and expose sensitive data.  
- Server-side path validation and strict access controls are essential for preventing path traversal attacks.

