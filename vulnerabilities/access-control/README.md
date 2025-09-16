# Access Control Labs

This folder contains documentation of selected **Access Control** labs from the PortSwigger Web Security Academy. While I have completed many labs on access control vulnerabilities, the ones included here illustrate a diverse range of exploitation scenarios and common pitfalls.

## Labs included

1. **Lab 01 — Unprotected Admin Functionality with Unpredictable URL**
   Explores how administrative endpoints hidden by obscurity (unpredictable URLs) can still be accessed without proper authentication.

   * File: `featured/lab-01-unprotected-admin-url.md`

2. **Lab 02 — User Role Can Be Modified in User Profile**
   Demonstrates insecure direct object manipulation where a user can escalate privileges by modifying their role through profile updates.

   * File: `lab-02-user-role-modification.md`

3. **Lab 03 — User ID Controlled by Request Parameter with Data Leakage in Redirect**
   Shows how manipulating user-specific request parameters can leak sensitive data belonging to other users.

   * File: `lab-03-userid-parameter-data-leak.md`

4. **Lab 04 — Referer-Based Access Control**
   Highlights the insecurity of relying on the HTTP Referer header for access control and demonstrates how it can be bypassed to perform unauthorized administrative actions.

   * File: `lab-04-referer-based-access.md`

## Lessons and insights

* Hidden URLs, client-controllable parameters, and client headers are insufficient for enforcing access control.
* Proper server-side authentication and authorization checks are essential for all administrative functionality.
* These four labs provide a good diversity of access control issues, from insecure direct object references and role manipulation to header-based and URL-based weaknesses.
* Logging, monitoring, and consistent RBAC implementation are key mitigation strategies to prevent unauthorized access.
