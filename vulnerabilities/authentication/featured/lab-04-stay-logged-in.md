# Lab 04 — Brute-Forcing a “Stay-Logged-In” Cookie (featured)

**Vulnerability:** Authentication — Session Management / Insecure Cookie  
**Lab source:** PortSwigger Web Security Academy  
**Date completed:** YYYY-MM-DD

---

## Short overview
In this lab, I explored how weakly constructed “stay-logged-in” cookies could be brute-forced to gain unauthorized access to other accounts. By analyzing the cookie structure and applying payload processing in Burp Intruder, I was able to identify a valid cookie for another user.

## Goal
- Understand how session cookies can be insecurely implemented.  
- Brute-force a “stay-logged-in” cookie to access a target account in the lab environment.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater, Intruder)

---

## My steps (narrative, sanitized)

1. I logged in to my own account with the **Stay logged in** option enabled. This set a stay-logged-in cookie in the browser. I inspected it in Burp Suite and noticed it was Base64-encoded. Decoding it revealed a string in the format `username:md5Hash`, suggesting the cookie included the username and an MD5 hash of the password.

2. To confirm the structure, I hashed my password using MD5 and observed that the hash matched the cookie value. This indicated the cookie was constructed as:
   
3. I logged out and captured a GET `/my-account?id=wiener` request containing the stay-logged-in cookie. I sent this request to Burp Intruder, using the cookie as a payload position.  

4. I added my password as a single payload and configured **Payload Processing** rules:  
- Hash: MD5  
- Add prefix: [the name]  
- Encode: Base64  

5. I added a **grep match rule** to detect the presence of the **Update email** button in the response, which indicates successful authentication. Running the attack confirmed that the processing rules generated a valid cookie for my own account.

6. Next, I adapted the attack to target another user:  
- Replaced my password with a list of candidate passwords.  
- Changed the `id` parameter in the request URL to the target user.  
- Adjusted the **Add prefix** rule to the target username.  

7. I ran the attack and found a single request whose response contained the **Update email** button, revealing the valid stay-logged-in cookie for the target account. Logging in with this cookie confirmed access and solved the lab.

---

## My observations (sanitized)
- The stay-logged-in cookie was weakly protected and predictable.  
- Payload processing in Burp Intruder allowed me to generate valid cookies for accounts without knowing their passwords.  
- The attack successfully authenticated me to another user’s account in the lab environment.

`images/lab-04-stay-logged-in.png`

---

## Mitigation
- Generate secure, unpredictable session cookies using cryptographically strong randomness.  
- Avoid including sensitive information (like password hashes) in client-side cookies.  
- Implement short-lived session tokens and require re-authentication for sensitive operations.  
- Monitor for abnormal usage patterns, such as repeated failed cookie attempts.

---

## Lessons learned
- Weak cookie design can lead to account compromise even without password knowledge.  
- Burp Intruder’s payload processing can be used to safely test and exploit session vulnerabilities in lab environments.  
- Persistent login features must be carefully implemented to prevent abuse.



