# Lab 02 — File Path Traversal: Double-Encoded Traversal Bypass (featured)

**Vulnerability:** Path Traversal — URL-Encoding Bypass  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored how web applications can be tricked into returning sensitive files by using double-encoded traversal sequences. Even when single traversal sequences are filtered or stripped, encoding techniques can bypass protections.

## Goal
- Access system files by bypassing input validation using double-encoded paths.  
- Understand the importance of correctly decoding and validating user input.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I started by examining how the application retrieved product images and intercepted a request using Burp Suite. The request contained a `filename` parameter specifying the image to load.  

To bypass the server’s filtering, I replaced the filename with a double-encoded traversal path: 
After sending the modified request, I observed that the server returned the contents of `/etc/passwd`. This confirmed that the server decoded the input and allowed traversal despite filtering, illustrating a vulnerability to double-encoded path attacks.

---

## My observations (sanitized)
- Double-encoding allowed traversal sequences to bypass server-side filtering.  
- The attack exposed sensitive system files, demonstrating inadequate input handling.  
- URL-decoding vulnerabilities can defeat naive security measures intended to block traversal.

 `images/lab-02-double-encoded-bypass.png` 

---

## Mitigation
- Properly normalize and decode all input before validation.  
- Apply strict whitelisting for allowed file paths and filenames.  
- Never rely solely on string filtering to prevent traversal attacks.  
- Limit file access to a designated safe directory.

---

## Lessons learned
- Input encoding can be exploited to bypass insufficient server validation.  
- Always handle URL decoding carefully before validating file paths.  
- Comprehensive input normalization and validation are critical to prevent path traversal attacks.


