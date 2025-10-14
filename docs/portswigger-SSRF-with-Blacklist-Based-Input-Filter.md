# 🛡️ Web Security Academy Lab: SSRF with Blacklist-Based Input Filter
## 🧠 Objective
Exploit a Server-Side Request Forgery (SSRF) vulnerability that uses a blacklist-based input filter to bypass restrictions and access internal admin functionality.

## 🧩 Step-by-Step Exploitation Walkthrough
### 🔍 Step 1: Reconnaissance

I began by analyzing the product page, which included a “Check stock” feature. This triggered a POST request to /product/stock, with a stockApi parameter pointing to an external service. This hinted at SSRF potential.

<img width="929" height="794" alt="Screenshot 2025-10-12 174029" src="https://github.com/user-attachments/assets/ab795ed9-6eb0-4ee0-a870-e4d5c8e01425" />

<img width="634" height="218" alt="Screenshot 2025-10-12 174103" src="https://github.com/user-attachments/assets/4b058bae-0e99-4f68-998d-d491e3196d06" />

### 🧪 Step 2: Intercepting the Request

Burp Suite's HTTP history is showing a POST to /product/stock Using Burp Suite, I intercepted the request and found the stockApi parameter pointing to:
```
https://stock.weliketoshop.net:443/product/stock/check?productId=1&storeId=1
```

<img width="818" height="397" alt="Screenshot 2025-10-12 174200" src="https://github.com/user-attachments/assets/e8a04214-659f-4502-aacc-0e624e7e404c" />

<img width="488" height="500" alt="Screenshot 2025-10-12 174224" src="https://github.com/user-attachments/assets/85771703-686a-4618-974f-a7d8e924f8a8" />

This confirmed that the server was making outbound requests based on user input.

### 🧬 Step 3: Testing Localhost Access

Modified request with:

```
stockApi=http://127.0.0.1/admin
```

This was blocked by the blacklist filter, returning:
"External stock check blocked for security reasons"

<img width="493" height="200" alt="Screenshot 2025-10-12 174741" src="https://github.com/user-attachments/assets/50e72fc7-e931-46a7-ac28-c21aa5365c73" />

### 🧪 Step 4: Bypassing the Blacklist

Encoded payloads like:
```
http%3A%2F%2F127.1%2Fadmin
```

<img width="488" height="500" alt="Screenshot 2025-10-12 174710" src="https://github.com/user-attachments/assets/5897dfd9-eebc-4bed-a6df-b4248177ce6c" />

and
```
http://127.1%2F%2561dmin
```

<img width="796" height="425" alt="Screenshot 2025-10-12 180400" src="https://github.com/user-attachments/assets/08999027-6963-4c92-ba47-44a591f96dfe" />

I used techniques like:

Alternate IP representations (127.1, 2130706433)

Double URL encoding (%2561dmin → %61dmin → admin) These payloads bypassed the blacklist and reached internal endpoints.

<img width="486" height="333" alt="Screenshot 2025-10-12 181458" src="https://github.com/user-attachments/assets/a8f67ae7-64c9-42e5-bd53-c3333cea111d" />

### 🎯 Step 5: Triggering the Admin Action

POST request to:
```
stockApi=http://127.1/admin/delete?username=carlos
```

<img width="785" height="480" alt="Screenshot 2025-10-12 181548" src="https://github.com/user-attachments/assets/385b3d1e-5b9b-4b89-ad79-3acd2556d553" />

This SSRF payload successfully reached the internal admin panel and triggered the deletion of the user carlos.

### ✅ Step 6: Lab Completion

The lab confirmed successful exploitation and marked the challenge as complete.

<img width="1782" height="787" alt="Screenshot 2025-10-12 181611" src="https://github.com/user-attachments/assets/e2d8d907-c15f-4e94-8513-63a8ddffb7c1" />

### 🧠 Key Takeaways
Blacklists are weak defenses against SSRF; attackers can use encoding tricks and alternate IP formats.

SSRF can be used to access internal services and perform unauthorized actions.

Always validate and sanitize user input, and use allowlists instead of blacklists.

### 📁 Repository Notes
This writeup is part of my ongoing journey in ethical hacking and web application security. You can find more CTF solutions and lab walkthroughs in my GitHub Portfolio.
