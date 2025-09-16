# Lab 01 — Excessive Trust in Client-Side Controls (featured)

**Vulnerability:** Business Logic — Excessive trust in client-side controls (price manipulation)  
**Lab source:** PortSwigger Web Security Academy  

---

## Short overview
This lab demonstrates the risk of trusting client-side data for critical decisions. When the server accepts price values supplied by the client, attackers can manipulate purchase flows and bypass business rules (such as insufficient credit checks).

## Goal
- Verify whether the application relies on client-provided price data for order processing.  
- Manipulate the cart price to complete a purchase when store credit would otherwise be insufficient.

## Environment & tools
- Lab: PortSwigger Web Security Academy (controlled lab)  
- Tools: Browser + Burp Suite (Proxy, Repeater)

---

## My steps (narrative, sanitized)

I logged into the lab account and attempted to buy the leather jacket, but the checkout failed because my store credit was insufficient. With Burp running I captured the sequence of requests made while adding the item to my cart and progressing toward checkout.

Inspecting the HTTP history, I found the `POST /cart` request that adds an item to the cart and noticed a `price` parameter in the request body. To test how the server treated that value, I sent the `POST /cart` request to **Burp Repeater** and edited the `price` parameter to a lower integer. After forwarding the modified request and refreshing the cart in the browser, the cart reflected my edited price.

I repeated the process, lowering the price until it was below my available store credit. With the artificially reduced price in place, I completed the checkout flow and confirmed the order succeeded — demonstrating that the server trusted the client-supplied price.

---

## What I observed (sanitized)
- The cart/order flow accepted a client-supplied `price` value and used it in purchase calculations.  
- Manipulating the `price` parameter on the client request allowed me to bypass the insufficient-credit check and place an order.  
- The behavior was consistent and reproducible in the lab environment.

: `images/lab-01-client-side-trust.png`

---

## Mitigation
- Never trust client-side values for critical business logic. Server-side authoritative calculation should be the source of truth (e.g., look up product prices on the server by product ID).  
- Validate and re-calculate prices on the server side before accepting payment.  
- Use server-side checks to enforce business rules (credit limits, inventory, discounts) and ignore client-supplied totals.  
- Implement logging/alerting for unexpected price changes or anomalies in order requests.

---

## Lessons learned
- Client-side controls are convenient for the UI but are not a security boundary.  
- Business rules must be enforced server-side; otherwise, simple request tampering can break them.  


