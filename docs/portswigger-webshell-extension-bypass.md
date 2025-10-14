**🛡️ Web Security Academy Lab:** Web Shell Upload via Extension Blacklist Bypass
🧠 Objective
The goal of this lab was to bypass a server-side blacklist that restricts certain file extensions (e.g., .php) and successfully upload a web shell to extract sensitive information.

**🧩 Step-by-Step Exploitation Walkthrough**

**🔍 Step 1: Initial Upload Attempt Fails**
Screenshot Reference: "Sorry, php files are not allowed"

<img width="857" height="603" alt="Screenshot 2025-10-07 124958" src="https://github.com/user-attachments/assets/e585e2b5-ab8f-4f72-bebc-2448f783ea4e" />

<img width="505" height="120" alt="Screenshot 2025-10-06 132341" src="https://github.com/user-attachments/assets/45d39cb1-37c0-4af0-bc0f-4fbd71a4a6bb" />

<img width="847" height="663" alt="Screenshot 2025-10-07 125722" src="https://github.com/user-attachments/assets/d4920f39-e80b-4562-a854-f72c678cb077" />


I began by attempting to upload a basic PHP web shell (exploit.php) via the avatar upload functionality. The server responded with an error message indicating that .php files were blocked. This confirmed the presence of an extension-based blacklist.

**🧪 Step 2: Inspecting the Upload Mechanism**
Screenshot Reference: Burp Suite - HTTP History and Request to /my-account/avatar
<img width="855" height="624" alt="Screenshot 2025-10-07 124643" src="https://github.com/user-attachments/assets/cbec31e0-be56-4441-acac-ce60ceb6bd74" />

<img width="526" height="282" alt="Screenshot 2025-10-07 124610" src="https://github.com/user-attachments/assets/8414060d-c28e-4962-a1c3-54e41f92b0f1" />


Using Burp Suite, I intercepted the avatar upload request. The multipart/form-data payload revealed the structure of the file upload, including the filename and content-type headers. This gave me insight into how the server processes uploads.

**🧬 Step 3: Attempting a Bypass with .php.jpg**
Screenshot Reference: Burp Suite - Repeater tab with shrek.jpg

<img width="349" height="167" alt="Screenshot 2025-10-13 194513" src="https://github.com/user-attachments/assets/e281e9f2-f89e-4a09-b8db-01655834e05b" />

<img width="340" height="107" alt="Screenshot 2025-10-13 194534" src="https://github.com/user-attachments/assets/f077d6f5-629b-4ddf-a59b-03223b32cd8f" />

<img width="301" height="199" alt="Screenshot 2025-10-13 194015" src="https://github.com/user-attachments/assets/72125acb-13e4-4bdc-9e01-3ee77145d057" />

<img width="323" height="301" alt="Screenshot 2025-10-13 194040" src="https://github.com/user-attachments/assets/696cf90a-a870-4456-afc3-a65ae8a78413" />

<img width="938" height="679" alt="Screenshot 2025-10-13 194110" src="https://github.com/user-attachments/assets/1e6783b1-beb3-47c2-85ac-c330e19d0b06" />


I modified the filename to shrek.php.jpg, hoping the server would treat it as a harmless image while still executing the embedded PHP code. However, the server did not execute the payload, indicating that it likely validated the file content or used a stricter blacklist.

**🧱 Step 4: Uploading .htaccess to Override MIME Handling**
Screenshot Reference: Burp Suite - POST request with .htaccess

<img width="863" height="654" alt="Screenshot 2025-10-07 125859" src="https://github.com/user-attachments/assets/9472822d-5269-4245-8658-77759e187f8d" />

<img width="859" height="641" alt="Screenshot 2025-10-07 125924" src="https://github.com/user-attachments/assets/739cf87f-5c9b-4f7a-bb90-ce7109553ed5" />


To force the server to treat .jpg files as PHP, I uploaded a .htaccess file with the following content:

apache
AddType application/x-httpd-php .exploit
This instructs the server to interpret .exploit files in the directory as PHP scripts

**🧠 Step 5: Uploading the Web Shell as shrek.exploit**
Screenshot Reference: Burp Suite - Repeater with shrek. payload

<img width="859" height="638" alt="Screenshot 2025-10-07 130146" src="https://github.com/user-attachments/assets/9fbefe62-e724-4881-8212-6ee813147e06" />

<img width="858" height="667" alt="Screenshot 2025-10-07 130238" src="https://github.com/user-attachments/assets/b798974a-d8a9-4334-9be4-96bae7b8dd06" />


With the .htaccess file in place, I re-uploaded the web shell disguised as shrek.exploit. The payload contained:

php
<?php echo file_get_contents('/home/carlos/secret'); ?>
This PHP code attempts to read the contents of a sensitive file on the server.

✅ Step 6: Successful Execution and Flag Retrieval
Screenshot Reference: Burp Suite - Response with secret string

<img width="718" height="466" alt="Screenshot 2025-10-07 130337" src="https://github.com/user-attachments/assets/3dff8016-2608-42ee-a16a-7b503f79eae2" />


After navigating to the uploaded file by right-clicking on the broken avatar image and opening it in a new tab, the server executed the PHP code and returned the contents of /home/carlos/secret, revealing the flag:

<img width="500" height="234" alt="Screenshot 2025-10-07 130406" src="https://github.com/user-attachments/assets/42487fc9-c65d-49c2-8fae-641fb2a9914b" />

🏁 Step 7: Submitting the Flag
Screenshot Reference: Dialog box with flag input

<img width="701" height="349" alt="Screenshot 2025-10-07 130444" src="https://github.com/user-attachments/assets/9fec86dc-536a-4c69-a6c8-c648c3e3ba71" />

I submitted the retrieved flag through the lab interface, confirming successful exploitation.

🎉 Step 8: Lab Completion
Screenshot Reference: "Congratulations, you solved the lab!"

<img width="951" height="381" alt="Screenshot 2025-10-07 130504" src="https://github.com/user-attachments/assets/64fc37ef-fbb0-4b18-b46e-cd71280ec12a" />

The lab acknowledged the successful bypass and payload execution, marking the challenge as complete.

🧠 Key Takeaways
File extension blacklists can be bypassed using .htaccess overrides.

MIME type and content validation are critical for secure file upload handling.

Burp Suite is an essential tool for intercepting, modifying, and replaying HTTP requests during web application testing.

📁 Repository Notes
This writeup is part of my ongoing journey in web application security. You can find more CTF solutions and lab walkthroughs in my GitHub Portfolio.
