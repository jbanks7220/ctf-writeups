# 🛡️ Web Security Academy Lab: Basic SSRF Against the Local Server
## 🧠 Objective
Exploit a Server-Side Request Forgery vulnerability to interact with internal services and perform unauthorized actions—specifically, delete the user carlos.

## 🧩 Step-by-Step Exploitation Walkthrough
### 🔍 Step 1: Reconnaissance

I began by analyzing the product page, which included a dropdown for store locations and a “Check stock” button. This triggered a POST request to /product/stock, suggesting a backend API call.

<img width="918" height="769" alt="Screenshot 2025-10-12 172101" src="https://github.com/user-attachments/assets/e54af90b-6533-40c3-8c81-ca0f9803f746" />

<img width="572" height="172" alt="Screenshot 2025-10-12 172129" src="https://github.com/user-attachments/assets/e16a3e13-e056-4a9c-a5c9-7e5da8e89f1a" />

### 🧪 Step 2: Intercepting the Request

I intercepted the request and found the stockApi parameter pointing to an external service:

<img width="830" height="336" alt="Screenshot 2025-10-12 172217" src="https://github.com/user-attachments/assets/cb1ff381-6759-4a01-8202-4e8403745873" />

```
stockApi=http://stock.weliketoshop.net:8080/product/stock/&productId=2&storeId=1
```

<img width="829" height="725" alt="Screenshot 2025-10-12 172247" src="https://github.com/user-attachments/assets/f3fc2933-8cd4-4fdf-92af-c6dfb9a86cd3" />

This indicated that the server was making a request to the URL provided—classic SSRF behavior.

### 🧬 Step 3: Redirecting to Internal Services

I changed the stockApi parameter to point to http://localhost/admin, attempting to access internal services not exposed to the public.

<img width="487" height="507" alt="Screenshot 2025-10-12 172328" src="https://github.com/user-attachments/assets/b8e55c72-9c6f-4cc0-bf99-2bf142940881" />

<img width="842" height="722" alt="Screenshot 2025-10-12 172457" src="https://github.com/user-attachments/assets/534d83bd-4e98-421f-8815-09c739e2fc1a" />

### 🎯 Step 4: Targeting the Admin Endpoint

This payload attempted to delete the user carlos by exploiting the SSRF to reach the internal admin panel.

```
stockApi=http://localhost/admin/delete?username=carlos
```

<img width="485" height="498" alt="Screenshot 2025-10-12 172557" src="https://github.com/user-attachments/assets/c133d660-86d3-4ffe-9611-5956b308798c" />

### ✅ Step 5: Confirming the Exploit

The server should respond with a success message, confirming that the SSRF was successful and the internal admin endpoint was triggered. in my case, this lab was already completed so instead it returned a "302 Found" status code, meaning that carlos had been temporarily removed.

<img width="498" height="254" alt="Screenshot 2025-10-14 125011" src="https://github.com/user-attachments/assets/b1805acd-0dab-4eca-831e-0e19fce541f4" />

### 🏁 Step 6: Lab Completion

The lab validated the exploit and marked it as complete.

<img width="712" height="208" alt="image" src="https://github.com/user-attachments/assets/573c8871-e976-4d11-8cef-23121b066ef0" />

### 🧠 Key Takeaways
SSRF vulnerabilities allow attackers to make requests from the server to internal or protected resources.

Always validate and restrict user-supplied URLs in backend requests.

Internal services should be protected with authentication and network segmentation.

### 📁 Repository Notes
This writeup is part of my ongoing journey in ethical hacking and web application security. You can find more CTF solutions and lab walkthroughs in my GitHub Portfolio.
