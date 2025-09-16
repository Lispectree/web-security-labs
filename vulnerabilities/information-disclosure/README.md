# Information Disclosure Labs

This folder contains documentation of selected **Information Disclosure** labs from the PortSwigger Web Security Academy. While I have completed other labs on information disclosure, the ones included here illustrate some of the most diverse and instructive scenarios.

## Labs included

1. **Lab 01 — Verbose Error Disclosure**
   Explores how unexpected input can cause the server to display full stack traces, revealing framework and version information.

   * File: `lab-01-verbose-error-disclosure.md`

2. **Lab 02 — Authentication Bypass via Information Disclosure**
   Demonstrates how an information disclosure flaw (e.g., reflected headers or sensitive files) can be leveraged to bypass authentication and gain admin access.

   * File: `lab-02-auth-bypass-via-info-disclosure.md`

## Lessons and insights

* Even seemingly low-impact information disclosures (stack traces, reflected headers, exposed backup files) can lead directly to authentication bypass or privilege escalation.
* Blind or indirect disclosures can provide attackers with enough intelligence to craft targeted exploits.
* Mitigation relies on both secure coding practices (proper error handling, server-side validation) and operational controls (removing backups, disabling unnecessary HTTP methods, and securing secret storage).
* These two labs provide a good diversity of information disclosure issues, from verbose errors to header-based authentication bypass.
