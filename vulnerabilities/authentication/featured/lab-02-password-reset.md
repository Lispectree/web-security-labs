
# Lab 02 — Password Reset / Broken Logic (featured)

**Vulnerability:** Authentication — Broken Password Reset Logic  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
This lab demonstrates how flaws in password reset functionality can allow attackers to change passwords for other accounts without proper authorization. Specifically, the reset token was not properly validated, allowing the attacker to submit arbitrary usernames and set passwords.

## Goal
- Exploit a password reset flaw to modify passwords of other accounts.  
- Understand how missing server-side validation of tokens can compromise account security.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy and Repeater) to inspect, modify, and replay requests.

---

## Step-by-step (sanitized, lab-only)

### 1) Inspect password reset flow
1. Click the **Forgot your password?** link and submit a test username.  
2. Observe the reset email sent in the lab environment, noting that a reset token is included as a URL query parameter.  
3. Intercept the POST request that submits the new password; the username is included as a hidden field.

### 2) Test token validation
1. Send the intercepted POST request to **Burp Repeater**.  
2. Remove the value of the reset token in both the URL and request body.  
3. Observe that the password reset still succeeds, confirming that the token is not being validated.

### 3) Exploit the flaw
1. Request another password reset in the lab.  
2. In Burp Repeater, remove the reset token again.  
3. Change the username parameter to a target account (lab environment only).  
4. Set a new password and send the request.  

### 4) Verify
- Log in to the target account using the new password in the controlled lab.  
- Access the user account page to confirm that the password reset was successful.

---

## What I observed (sanitized)
- The server allowed password resets without validating the token.  
- Changing the username in the request permitted control over other accounts in the lab environment.  
- The attack was reproducible across multiple attempts.

`images/lab-02-password-reset.png`

---

## Mitigation
- Always validate reset tokens on the server side before accepting new passwords.  
- Ensure reset tokens are single-use, time-limited, and tied to the correct user account.  
- Return generic responses to prevent revealing token validity or account existence.  
- Monitor and log all password reset attempts for suspicious activity.

---

## Lessons learned
- Password reset flows are high-risk areas that require strict server-side validation.  
- Failure to properly validate tokens can allow attackers to compromise other accounts.  
- Automated testing in a controlled lab is essential for safely identifying these flaws.
