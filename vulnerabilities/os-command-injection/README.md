# OS Command Injection

**Overview**  
OS Command Injection occurs when user-supplied input is improperly handled and incorporated into system commands. Attackers can execute arbitrary commands on the server, potentially exposing sensitive data or gaining control over the application.  

This section focuses on three featured labs from the PortSwigger Web Security Academy that illustrate different OS command injection techniques.

---

## Featured Labs

### 1. Basic Injection
**Objective:** Execute a simple system command to retrieve information from the server.  
**Key technique:** Injecting commands directly into a parameter that is passed to the operating system.  
**Lab file:** [`lab-01-basic-injection.md`](./featured/lab-01-basic-injection.md)

### 2. Blind Injection with Time Delays
**Objective:** Detect command execution when the server does not return command output.  
**Key technique:** Using time-based delays (`ping -c 10 127.0.0.1`) to infer successful injection.  
**Lab file:** [`lab-02-blind-time-delay.md`](./featured/lab-02-blind-time-delay.md)

### 3. Blind Injection with Output Redirection
**Objective:** Retrieve command output indirectly when responses do not display it.  
**Key technique:** Redirecting output to a file and then retrieving it via a normal application resource.  
**Lab file:** [`lab-03-output-redirection.md`](./featured/lab-03-output-redirection.md)

---

## Lessons learned
- OS Command Injection can be exploited even when output is not visible.  
- Blind techniques like time delays and output redirection are powerful tools for attackers.  
- Proper input validation, avoiding direct command execution, and enforcing least privileges are critical to prevent these attacks.

---

## Tools used
- Browser  
- Burp Suite (Proxy, Repeater, Intruder)

