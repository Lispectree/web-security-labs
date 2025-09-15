# Lab 02 — Blind OS Command Injection: Time Delays (featured)

**Vulnerability:** OS Command Injection — Blind Injection with Time Delays  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored a blind OS command injection scenario where the application did not display command output. Instead, timing differences were used to infer successful command execution.

## Goal
- Exploit blind command injection using time-based techniques.  
- Understand how attackers can detect vulnerabilities even when output is not returned.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I began by intercepting a request that submitted user feedback using Burp Suite. The request contained an `email` parameter, which normally accepted standard email addresses.  

To test for blind command injection, I injected a command designed to delay the server’s response:  
After sending the modified request, I noticed that the server took approximately 10 seconds to respond. This confirmed that my injected command was executed, even though no command output was displayed in the response.

---

## My observations (sanitized)
- The server executed injected commands without returning output, allowing exploitation to be detected through timing.  
- Time delays provided a reliable way to identify blind OS command injection.  
- Blind injections require careful observation of response behaviors rather than direct output.

 `images/lab-02-blind-time-delay.png

---

## Mitigation
- Properly sanitize and validate all user input.  
- Avoid passing user input to system commands whenever possible.  
- Implement least privilege for server processes to reduce impact.  
- Monitor for abnormal request patterns or unusual response times.

---

## Lessons learned
- Blind OS command injection can be identified without seeing direct output.  
- Time-based techniques provide attackers with valuable signals about server behavior.  
- Input validation and minimal privilege execution are key defenses against command injection.


