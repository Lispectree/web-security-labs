# Lab 03 — Web Shell Upload via Obfuscated File Extension (featured)

**Vulnerability:** File Upload — Insecure extension validation / null byte bypass  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how attackers can bypass file extension restrictions using obfuscated filenames and null byte injection. By manipulating the uploaded file’s name, an attacker can upload a PHP web shell disguised as an allowed image file, enabling execution of arbitrary code or access to sensitive data.

## Goal
- Understand how filename validation can be bypassed using null bytes or obfuscation techniques.  
- Upload and execute a malicious script to retrieve sensitive information in the controlled lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy   
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)
I logged in and uploaded an image as my avatar, then accessed my account page. In Burp, I navigated to Proxy → HTTP history and noticed that the image was fetched via a GET request to `/files/avatars/<YOUR-IMAGE>`. I sent this request to Burp Repeater. On my system, I created `exploit.php` containing a script to fetch Carlos’s secret, e.g., `<?php echo file_get_contents('/home/carlos/secret'); ?>`. Uploading this file normally was blocked due to allowed JPG and PNG restrictions. I sent the POST `/my-account/avatar` request to Repeater and modified the Content-Disposition header’s filename parameter to include a URL-encoded null byte followed by `.jpg`: `filename="exploit.php%00.jpg"`. After sending the request, the file was accepted by the server. Switching to the GET request in Repeater, I replaced the file path with `exploit.php` and sent it, retrieving Carlos’s secret. I submitted the secret to solve the lab. All sensitive data was sanitized in saved artifacts.

---

## What I observed
- The server relied on the file extension for validation, which was bypassed using a null byte.  
- Despite appearing as an allowed image, the uploaded file executed as PHP.  
- The GET request to the uploaded file returned sensitive content (Carlos’s secret) without further checks.

---

## Proof 
- Screenshot of the POST `/my-account/avatar` request in Burp Repeater showing the obfuscated filename.  
- Response from the GET request returning Carlos’s secret (sanitized).  



---

## Mitigation
- Validate files by content (magic numbers) rather than filename or extension alone.  
- Properly handle null bytes and other encoding tricks in filenames.  
- Disable execution of uploaded files in web-accessible directories.  
- Apply server-side authorization checks and sandbox uploaded files.

---

## Lessons learned
- Filename obfuscation and null byte injection can bypass naive extension-based filters.  
- File upload controls must be robust and enforced server-side.  
- Safe storage and execution restrictions are critical to prevent remote code execution and sensitive data exposure.
