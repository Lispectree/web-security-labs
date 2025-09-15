# Lab 01 — Username Enumeration via Different Responses (featured)

**Vulnerability:** Authentication — Username Enumeration  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored how subtle differences in server responses can reveal valid usernames. Even when error messages appear generic, some responses leak information about account existence.

## Goal
- Identify valid usernames using response differences.  
- Understand how attackers can infer account existence from server behavior.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Intruder)

---

## My steps (narrative, sanitized)

1. I started by submitting invalid credentials on the login page while Burp Suite was capturing requests.  
2. I sent the POST `/login` request to **Burp Intruder** and observed that the username parameter was automatically highlighted as a payload position.  
3. I kept the password static and selected a **Sniper attack**, loading a list of candidate usernames.  
4. When the attack completed, I examined the **Length** column of the responses. Most invalid usernames returned identical responses, but one username triggered a different response: “Incorrect password.” This confirmed that the account existed.  
5. I repeated the test to verify consistency and noted the valid username.  

---

## My observations (sanitized)
- Differences in response messages revealed the existence of a valid account.  
- Automated testing with Burp Intruder helped quickly identify the valid username.  
- The response variation was subtle but consistent, making it a reliable indicator.

 `images/lab-01-username-enum.png` 

---

## Mitigation
- Always return generic error messages for failed login attempts.  
- Implement rate-limiting and account lockout policies to prevent automated enumeration.  
- Monitor login attempts for suspicious patterns.

---

## Lessons learned
- Even minor variations in server responses can leak sensitive information.  
- Automated tools allow safe, controlled testing of authentication flows.  
- Uniform error handling is essential to prevent username enumeration attacks.
