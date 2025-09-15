# Lab 03 — Blind OS Command Injection: Output Redirection (featured)

**Vulnerability:** OS Command Injection — Blind Injection with Output Redirection  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrated a blind OS command injection scenario where command output was not directly visible. By redirecting the output to a file, it became possible to retrieve results from injected commands.

## Goal
- Execute a command on the server and capture its output via file redirection.  
- Understand techniques to extract information when responses do not display command output directly.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I started by intercepting the feedback submission request using Burp Suite. The request included an `email` parameter, which I modified to inject a command and redirect its output to a file on the server:  
Upon sending this request, the response contained the output of the `whoami` command. This confirmed that my injection worked and that output redirection can be used to retrieve results from blind command injection.

---

## My observations (sanitized)
- The injected command was executed even though the response did not display output by default.  
- Output redirection allowed retrieval of command results through accessible application resources.  
- Blind injection can still leak critical information using indirect methods.

`images/lab-03-output-redirection.png`

---

## Mitigation
- Sanitize and validate all user-supplied input before it reaches system commands.  
- Avoid executing user input directly in system commands.  
- Restrict server file permissions to prevent writing sensitive output in web-accessible locations.  
- Monitor for unexpected file creation or modification activity.

---

## Lessons learned
- Output redirection can turn a blind OS command injection into a retrievable exploit.  
- Even when direct output is not visible, attackers can leverage indirect methods to obtain results.  
- Input validation, minimal privileges, and strict file permissions are essential defenses against OS command injection.

