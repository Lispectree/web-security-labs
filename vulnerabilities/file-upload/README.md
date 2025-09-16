# File Upload Labs

This folder contains documentation of selected **File Upload** labs from the PortSwigger Web Security Academy. While many file upload labs exist, the three included here illustrate diverse methods of bypassing restrictions and exploiting file upload functionality.

## Labs included

1. **Lab 01 — Web Shell Upload via Content-Type Restriction Bypass**
   Explores how modifying the Content-Type header allows malicious scripts to bypass MIME type restrictions and be executed.

   * File: `lab-01-webshell-contenttype-bypass.md`

2. **Lab 02 — Web Shell Upload via Extension Blacklist Bypass**
   Demonstrates bypassing extension blacklists using server configuration files (`.htaccess`) to execute arbitrary code.

   * File: `lab-02-webshell-extension-bypass.md`

3. **Lab 03 — Web Shell Upload via Obfuscated File Extension**
   Highlights null byte injection and filename obfuscation techniques to bypass extension validation and execute PHP scripts.

   * File: `lab-03-webshell-obfuscated-extension.md`

## Lessons and insights

* File upload functionality is a high-risk feature that can lead to remote code execution if not properly validated.
* Relying solely on MIME type or extension restrictions is insufficient to prevent malicious uploads.
* Techniques like Content-Type manipulation, server configuration abuse, and null byte injection can bypass naive validations.
* Proper server-side validation, safe file storage, execution restrictions, and sandboxing are critical to secure file upload implementations.
* These three labs provide a good diversity of file upload vulnerabilities, covering content-type, extension, and obfuscation bypass techniques.
