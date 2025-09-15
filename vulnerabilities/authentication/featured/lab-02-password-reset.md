# Lab 02 — Password Reset / Broken Logic (featured)

**Vulnerability:** Authentication — Broken Password Reset Logic  
**Lab source:** PortSwigger Web Security Academy  


---

## Short overview
In this lab, I explored a flaw in the password reset functionality that allowed me to reset passwords for other accounts without proper authorization. The reset token was not validated on the server side, which made this possible.

## Goal
- Exploit weak password reset logic to change passwords in a controlled lab environment.  
- Understand how missing server-side validation can compromise account security.

## Environment & tools
- Lab: PortSwigger Web Security Academy  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

1. I clicked the **Forgot your password?** link and submitted my own username. I viewed the password reset email sent by the lab and opened the reset link.  
2. I intercepted the POST request for submitting the new password in Burp Suite and noticed the reset token was included as a URL parameter. The username was a hidden input in the form.  
3. I sent this request to **Burp Repeater** and deleted the value of the reset token in both the URL and request body. When I submitted the request, the password reset still worked, confirming the token was not being validated.  
4. I requested another password reset and repeated the test to ensure the flaw was consistent.  
5. To exploit the flaw, I changed the username in the request to a target account and set a new password. Sending this request allowed me to reset the password for the target account in the controlled lab environment.  
6. I logged in to the target account using the newly set password and accessed the account page, successfully solving the lab.

---

## My observations (sanitized)
- The password reset token was not validated, allowing me to reset any account password.  
- Changing the username in the request enabled control over other accounts in the lab environment.  
- The attack was reproducible and consistent.

 `images/lab-02-password-reset.png`

---

## Mitigation
- Validate reset tokens on the server side before accepting new passwords.  
- Use single-use, time-limited tokens tied to the correct account.  
- Return generic responses to avoid revealing token validity or account existence.  
- Monitor and log suspicious password reset attempts.

---

## Lessons learned
- Password reset flows are sensitive areas that require strict server-side validation.  
- Failing to validate tokens can compromise account security.  
- Controlled lab testing allows safe exploitation of logic flaws to understand their impact.
