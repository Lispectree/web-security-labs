# Lab 01 — Web Shell Upload via Content-Type Restriction Bypass

**Vulnerability:** File Upload — Insecure file type validation / Content-Type bypass  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how client-side or MIME-type based restrictions on file uploads can be bypassed. By modifying the Content-Type in a POST request, an attacker can upload a malicious script disguised as an allowed image file, potentially executing arbitrary code or retrieving sensitive data.

## Goal
- Verify whether server-side validation properly enforces allowed file types.  
- Upload a web shell or malicious script by bypassing the MIME type restriction and use it to access sensitive data in the controlled lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)
I logged in and uploaded an image as my avatar, then accessed my account page. In Burp, I navigated to Proxy → HTTP history and noticed that the image was fetched via a GET request to `/files/avatars/<YOUR-IMAGE>`. I sent this request to Burp Repeater. On my system, I created `exploit.php` containing a script to fetch Carlos’s secret, for example: `<?php echo file_get_contents('/home/carlos/secret'); ?>`. Attempting to upload this file normally was blocked due to MIME type restrictions (allowed only image/jpeg or image/png). In Burp, I located the POST `/my-account/avatar` request used for uploading files, sent it to Repeater, and modified the Content-Type of the uploaded file to `image/jpeg`. After resending the request, the server accepted the file. Switching to the GET request in Repeater, I replaced the file path with `exploit.php` and sent it. The response contained Carlos’s secret, which I submitted to solve the lab.

---

## What I observed (sanitized)
- The server relied solely on the Content-Type provided in the request to validate uploaded files.  
- Modifying the Content-Type allowed arbitrary scripts to be uploaded and executed.  
- The GET request to the uploaded file returned sensitive content without further access control.

---

## Proof (sanitized)
- Screenshot of the POST `/my-account/avatar` request in Burp Repeater with Content-Type modified.  
- Response from the GET request showing Carlos’s secret (sanitized).  


---

## Mitigation
- Validate uploaded files based on file content (magic numbers) rather than MIME type or file extension alone.  
- Restrict uploaded files to safe directories and prevent execution of user-supplied scripts.  
- Apply server-side authentication and authorization checks when accessing uploaded files.  
- Consider using random filenames and sandboxed directories to reduce risk.

---

## Lessons learned
- Client-controllable metadata like Content-Type can be manipulated to bypass naive file upload restrictions.  
- File upload functionality is a high-risk attack vector; robust server-side validation and safe storage practices are essential.  
- Even seemingly innocuous features, like avatar uploads, can expose critical secrets if not properly controlled.
