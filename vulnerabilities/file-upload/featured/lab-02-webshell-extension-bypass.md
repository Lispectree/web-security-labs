# Lab 02 — Web Shell Upload via Extension Blacklist Bypass (featured)

**Vulnerability:** File Upload — Insecure extension filtering / server configuration bypass  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how an extension blacklist can be bypassed to upload and execute malicious scripts. By manipulating both the filename and the server configuration using `.htaccess`, an attacker can execute arbitrary PHP code even when the system restricts certain file extensions.

## Goal
- Understand how extension blacklists can be bypassed by leveraging server behaviors.  
- Upload a web shell or malicious script disguised with an allowed extension and execute it to access sensitive data in the controlled lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, Repeater, HTTP history)

---

## My steps (narrative, exactly what I did)
I logged in and uploaded an image as my avatar, then accessed my account page. In Burp, I navigated to Proxy → HTTP history and noticed the image was fetched via a GET request to `/files/avatars/<YOUR-IMAGE>`. I sent this request to Burp Repeater. On my system, I created `exploit.php` containing a script to fetch Carlos’s secret, e.g., `<?php echo file_get_contents('/home/carlos/secret'); ?>`. Uploading this file normally was blocked due to the `.php` extension restriction. Inspecting the POST `/my-account/avatar` request in Burp, I observed the server was Apache. I sent the request to Repeater and modified it as follows: changed the filename to `.htaccess`, the Content-Type to `text/plain`, and replaced the file contents with `AddType application/x-httpd-php .l33t` to map a custom extension to PHP execution. After sending the `.htaccess` upload, I modified the original exploit filename to `exploit.l33t` and resent the request. The server accepted it. Using the GET request in Repeater, I replaced the file path with `exploit.l33t` and sent it, retrieving Carlos’s secret. I submitted the secret to solve the lab. All sensitive data was sanitized in saved artifacts.

---

## What I observed 
- The server blocked direct `.php` uploads but allowed other extensions.  
- By uploading a `.htaccess` file, I mapped a custom extension to be executed as PHP, bypassing the extension blacklist.  
- The malicious file executed successfully, exposing sensitive content (Carlos’s secret).

---

## Proof (sanitized)
  
- Response from the GET request returning Carlos’s secret 



---

## Mitigation
- Validate uploaded files based on file content rather than filename or extension alone.  
- Disable execution of uploaded files in web-accessible directories.  
- Do not allow clients to upload `.htaccess` or other server configuration files.  
- Apply strict server-side validation, sandboxing, and least privilege file storage.

---

## Lessons learned
- Extension blacklists can be bypassed using server-specific behaviors and configuration files.  
- Web servers like Apache can execute arbitrary extensions if configured improperly, highlighting the need for secure file upload handling.  
- Properly controlling uploaded file storage and execution is critical to prevent remote code execution.
