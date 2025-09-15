# Lab 01 — OS Command Injection: Simple Case (featured)

**Vulnerability:** OS Command Injection — Basic Injection  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored a basic OS command injection scenario where user input was directly incorporated into system commands. This allowed me to execute arbitrary commands on the server in a controlled lab environment.

## Goal
- Inject a simple system command to retrieve information from the server.  
- Understand how unsanitized user input can lead to command execution vulnerabilities.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I started by intercepting a request that checked the stock level for a specific store using Burp Suite. The request included a `storeID` parameter, which normally accepted numerical values.  

To test for command injection, I modified the `storeID` parameter to include a simple system command:  
After forwarding the modified request, I observed that the server’s response contained the username of the system account running the web application. This confirmed that the input was executed directly by the server, illustrating a basic command injection vulnerability.

---

## My observations (sanitized)
- Unsanitized input allowed me to execute arbitrary OS commands.  
- The server returned command output in the HTTP response, making exploitation straightforward.  
- Basic command injection is easily tested with simple operators like `|` in lab environments.

: `images/lab-01-basic-injection.png

---

## Mitigation
- Validate and sanitize all user-supplied input before passing it to system commands.  
- Use parameterized APIs or safe functions that avoid shell interpretation.  
- Apply the principle of least privilege to minimize damage if injection occurs.  
- Monitor and log unexpected command execution attempts.

---

## Lessons learned
- Directly including user input in system commands is extremely dangerous.  
- Even simple injections can reveal critical information about the system.  
- Input validation, safe APIs, and proper privilege management are essential defenses.

