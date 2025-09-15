# Lab 01 — Username Enumeration via Different Responses (featured)

**Vulnerability:** Authentication — Username Enumeration  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how an attacker can identify valid usernames by analyzing differences in server responses during login attempts. Even subtle variations in messages, HTTP status codes, or response lengths can leak account existence.

## Goal
- Identify valid usernames without access to credentials.  
- Understand how response differences reveal sensitive information in authentication flows.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy and Intruder) to capture requests, automate testing, and analyze response differences.

---

## Step-by-step (sanitized, lab-only)

### 1) Capture the login request
1. Visit the login page and submit invalid credentials.  
2. In Burp Suite, go to **Proxy > HTTP history** and locate the POST `/login` request.  
3. Highlight the username parameter and send it to **Burp Intruder**.

### 2) Identify valid usernames
1. In Intruder, the username parameter is automatically set as the payload position (`§username§`). Keep the password static.  
2. Use a **Sniper attack** with a **Simple list payload type**, loading candidate usernames.  
3. Start the attack and examine the **Length** (or response content) column in the results.  
4. Identify the username that triggers a response different from the others (e.g., “Incorrect password” vs “Invalid username”). This indicates a valid account.

### 3) Verify the account
1. Once the valid username is identified, you can optionally continue testing with password candidates in a controlled lab environment.  
2. Capture sanitized screenshots showing the response difference without exposing real credentials.

---

## What I observed (sanitized)
- Most invalid usernames produced a generic “Invalid username” message.  
- One username triggered a different response: “Incorrect password,” confirming the account exists.  
- The observation was consistent across repeated attempts.

 `images/lab-01-username-enum.png` —

---

## Mitigation
- Always return generic error messages for failed login attempts (e.g., “Invalid credentials”).  
- Implement rate-limiting and account lockout policies to prevent automated enumeration.  
- Monitor and log suspicious login attempts.

---

## Lessons learned
- Even minor differences in server responses can reveal user existence.  
- Automated tools like Burp Intruder make it easier to detect these differences in a controlled, safe environment.  
- Authentication flows must avoid giving attackers any clues about account validity.

