# 🛡️ Web Security Academy Labs: SQL Injection Exploitation
## 🧠 Objective
These labs demonstrate how SQL injection vulnerabilities can be exploited to:

Retrieve hidden data from a database.

### 🧩 SQL Injection in WHERE Clause – Retrieving Hidden Data
**🔍 Step 1: Identifying the Injection Point**

I began by analyzing the URL structure and noticed the category parameter was passed directly to the backend. This hinted at a potential injection point.

<img width="931" height="869" alt="Screenshot 2025-10-07 135039" src="https://github.com/user-attachments/assets/3f9085a6-e994-49fe-9b84-75dc17d6f27c" />

**🧪 Step 2: Testing for SQL Injection**

Injecting a single quote (') caused a server error, confirming that the input was being processed by a SQL query.

<img width="621" height="170" alt="Screenshot 2025-10-07 135115" src="https://github.com/user-attachments/assets/a710ba51-f17a-4e40-848d-eeb059f74801" />

**🧬 Step 3: Crafting the Payload**

I used the classic ' OR 1=1-- payload to bypass the WHERE clause condition. This forced the query to return all rows, including hidden data.

<img width="631" height="121" alt="Screenshot 2025-10-07 135138v2" src="https://github.com/user-attachments/assets/2fc9016e-a349-4d21-b3d8-3f6044b11a4c" />


**✅ Step 4: Successful Exploitation**

The lab confirmed that the injection worked and hidden product categories were revealed. Mission accomplished.

<img width="751" height="429" alt="Screenshot 2025-10-07 135138" src="https://github.com/user-attachments/assets/3072e560-4c65-447d-9a1a-47bd96f36e4c" />

### 🧠 Key Takeaways
SQL injection can expose hidden or unauthorized data.

Always validate and sanitize user input.

Even simple payloads can have serious consequences.

### 📁 Repository Notes
This writeup is part of my ongoing journey in ethical hacking and web application security. You can find more walkthroughs and lab solutions in my GitHub Portfolio.
