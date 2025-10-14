**🔐 SQL Injection – Login Bypass**

**🧠 Objective**
Demonstrate how SQL injection vulnerabilities can be exploited to:

Bypass authentication and gain unauthorized access.

**🔍 Step 1: Testing Login Behavior**
Screenshot Reference: Login screen with error message I attempted to log in using the username ' OR 1=1-- and a dummy password. The error message confirmed that the input was being processed by a SQL query.

<img width="717" height="312" alt="Screenshot 2025-10-07 140132" src="https://github.com/user-attachments/assets/5b856c18-e09a-432b-a8f1-31f45ecc1ae9" />


**🧪 Step 2: Exploiting the Vulnerability**
Screenshot Reference: Login screen with administrator'-- The payload terminated the SQL query early and commented out the password check, effectively bypassing authentication.

<img width="712" height="327" alt="Screenshot 2025-10-07 140223" src="https://github.com/user-attachments/assets/13fbaa44-92a8-40f5-83d4-3ebc5dbfd593" />

**✅ Step 3: Gaining Access**
Screenshot Reference: "My Account" page showing administrator access After submitting the payload, I was logged in as the administrator. The lab confirmed successful exploitation.

<img width="953" height="393" alt="Screenshot 2025-10-07 140242v2" src="https://github.com/user-attachments/assets/41ec5a13-3f84-4898-b98c-528b71b21b4d" />

**🏁 Step 4: Lab Completion**
Screenshot Reference: "Congratulations, you solved the lab!" The lab was marked as solved, validating the effectiveness of the SQL injection technique.

<img width="953" height="705" alt="Screenshot 2025-10-07 140242" src="https://github.com/user-attachments/assets/9ec5c725-ffef-4770-b733-4ee3525ed45c" />


**🧠 Key Takeaways**
SQL injection remains one of the most critical vulnerabilities in web applications.

Input validation and parameterized queries are essential defenses.

Even simple payloads like ' OR 1=1-- can lead to full data exposure or unauthorized access.

**📁 Repository Notes**
This writeup is part of my ongoing journey in ethical hacking and web application security. You can find more walkthroughs and lab solutions in my GitHub Portfolio.
