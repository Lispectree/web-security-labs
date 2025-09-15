# Lab 03 — File Path Traversal: Null Byte Bypass (featured)

**Vulnerability:** Path Traversal — File Extension Validation Bypass  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I investigated how appending a null byte (`%00`) can bypass file extension validation. This technique allows attackers to access sensitive files even when the server restricts certain extensions.

## Goal
- Retrieve system files despite restrictions on file extensions.  
- Demonstrate how null byte injection can circumvent simple validation logic.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I began by intercepting a request that fetched product images using Burp Suite. The request included a `filename` parameter, which was normally used to specify the image to load.  

To bypass the server’s extension checks, I modified the filename parameter to include a null byte followed by a disallowed extension:  
Sending the modified request revealed the contents of `/etc/passwd`. This confirmed that the server’s validation only checked the file extension and that the null byte terminated the filename early, allowing access to restricted files.

---

## My observations (sanitized)
- Null byte injection bypassed the server’s file extension check.  
- Sensitive system files were exposed despite extension restrictions.  
- File validation based solely on extensions is insufficient to prevent traversal attacks.

`images/lab-03-null-byte-bypass.png` 

---

## Mitigation
- Avoid relying solely on file extensions to validate user-supplied filenames.  
- Normalize and canonicalize file paths before performing validation.  
- Restrict file access to a safe, predefined directory.  
- Use secure API functions that prevent null byte truncation issues.

---

## Lessons learned
- Null bytes can terminate strings unexpectedly, bypassing naive file validation.  
- Proper path handling and canonicalization are crucial to prevent traversal.  
- Security controls must consider edge cases like encoded characters and special terminators.


