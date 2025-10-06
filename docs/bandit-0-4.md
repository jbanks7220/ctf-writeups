# Bandit — Levels 0 → 4

**Platform:** OverTheWire — Bandit
**Date:** 2025-10-06
**Author:** Jamir Banks (github.com/jbanks7220)

---

## TL;DR

Completed Bandit levels 0–4. This writeup documents the exact steps, commands, and reasoning I used to progress th>

---

## Environment & Tools

* Host OS: Windows 10 (local) / WSL2 or Kali for practice (when needed)
* Tools: `ssh`, `ls`, `cat`, `file`, `strings`, `find`, basic shell commands (bash)
* Connection pattern used:

  ```bash
  ssh banditX@bandit.labs.overthewire.org -p 2220
  ```

  (Replace `X` with the level user number and use the password retrieved from the previous level.)

---

# Level 0 → Level 1

### Goal

Retrieve the password for the `bandit1` account from the `bandit0` environment.

### Methodology

Start by connecting to the server and doing basic file enumeration in the home directory.

### Commands & Output

```bash
# connect as bandit0
ssh bandit0@bandit.labs.overthewire.org -p 2220
# once connected, list files
ls -la
# view the obvious file that is typically present
cat readme
```
<img width="873" height="357" alt="Screenshot 2025-10-06 093126" src="https://github.com/user-attachments/assets/99f7396b-8707-4728-8fb0-a5d9ca8f5608" />

<img width="507" height="159" alt="Screenshot 2025-10-06 093207" src="https://github.com/user-attachments/assets/d59f929b-966e-47c0-95fb-0bfdac3a3a5c" />

<img width="681" height="166" alt="Screenshot 2025-10-06 093245" src="https://github.com/user-attachments/assets/59cd82aa-5223-4c75-9232-2526ad7b9886" />



Example (trimmed) output:

```
$ ls -la
-rw-r--r-- 1 bandit0 bandit0 103 Oct 6 12:00 readme

$ cat readme
The password for the next level is: <ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If>
```

### Result

I retrieved the password for `bandit1` by reading `readme` and used that password for the next SSH login.

### Notes / Lessons

* Basic enumeration (`ls`, `cat`) is the first step.
* Defensive note: avoid leaving plaintext secrets in publicly readable files.

---

# Level 1 → Level 2

### Goal

Obtain the password for `bandit2` from the `bandit1` environment.

### Methodology

Look for oddly-named files (including files whose name starts with `-`), check file listings and read any non-stan>

### Commands & Output

```bash
# ssh into level 1 (use password from level 0)
ssh bandit1@bandit.labs.overthewire.org -p 2220

# examine files
ls -la

# there is a file named "-" (single hyphen). To 'cat' a file named '-' require explicit path:
cat ./-
```

<img width="585" height="246" alt="Screenshot 2025-10-06 093533" src="https://github.com/user-attachments/assets/196c21ba-ebcf-4552-b8ce-3202b808805f" />

<img width="505" height="151" alt="Screenshot 2025-10-06 093618" src="https://github.com/user-attachments/assets/15cfd054-81eb-4bc5-803e-05b6aa9d2845" />

<img width="269" height="68" alt="Screenshot 2025-10-06 093720" src="https://github.com/user-attachments/assets/5111b86c-1354-4864-a5a3-f8ca8474fee8" />


Example (trimmed) output:

```
$ ls -la
-rw-r--r-- 1 bandit1 bandit1 33 Oct 6 12:00 -

$ cat ./-
<263JGJPfgU6LtdEvgfWU1XP5yac29mFx>
```

### Result

Used `cat ./-` to read the file literally named `-` and retrieved the `bandit2` password.

### Notes / Lessons

* Filenames that begin with `-` can be treated as options by many commands, so use `./-` to reference them safely.
* Defensive note: special-character filenames can be abused or cause confusion — sanitize uploaded filenames in re>

---

# Level 2 → Level 3

### Goal

Find the password for `bandit3` from `bandit2`'s home directory.

### Methodology

Look for files with spaces in their names or other odd naming conventions, then cat the file using quoting or esca>

### Commands & Output

```bash
# connect as bandit2
ssh bandit2@bandit.labs.overthewire.org -p 2220

# list files (note files with spaces)
ls -la

# if a file with spaces in its name exists (example: --spaces in this filename--), read it by referencing the full>
cat /home/bandit2/'--spaces in this filename--'
```

<img width="592" height="242" alt="Screenshot 2025-10-06 093937" src="https://github.com/user-attachments/assets/a080bca5-6098-452e-b0ec-654c9758ce2a" />
<img width="618" height="183" alt="Screenshot 2025-10-06 095200" src="https://github.com/user-attachments/assets/70408311-9f36-457f-a703-a05de384278e" />



Example (trimmed) output:

```
$ ls -la
-rw-r--r-- 1 bandit2 bandit2 341 Oct 6 12:00 --spaces in this filename--

$ cat /home/bandit2/'--spaces in this filename--'
<MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx>
```

### Result

Retrieved the `bandit3` password by properly referencing a filename with spaces.

### Notes / Lessons

* Filenames with spaces must be quoted.
* Practical tip: when you see unusual filenames, `ls -la` and `file` help determine content and how to reference t>

---

# Level 3 → Level 4

### Goal

Retrieve the password for `bandit4` from the `bandit3` environment.

### Methodology

Search inside directories for hidden files or files with dot-prefixes. Use `ls -a` to reveal hidden files and then>

### Commands & Output

```bash
# ssh into level 3
ssh bandit3@bandit.labs.overthewire.org -p 2220

# list items including hidden
ls -la

# change into the target directory (often named 'inhere' or similar)
cd inhere
ls -la

# You should see a couple .files (files starting with .) and the one you want is named '...Hiding-From-You'
cat ./...Hiding-From-You
```

<img width="555" height="232" alt="Screenshot 2025-10-06 095550" src="https://github.com/user-attachments/assets/3ac13b6d-c41c-41d0-a325-b28016b15634" />
<img width="457" height="164" alt="Screenshot 2025-10-06 095637" src="https://github.com/user-attachments/assets/9e6ec819-de21-4484-b576-f7741a905cd1" />
<img width="533" height="134" alt="Screenshot 2025-10-06 095708" src="https://github.com/user-attachments/assets/d1ce4ca0-58ae-4d79-98c9-f40d4aef7da5" />
<img width="529" height="148" alt="Screenshot 2025-10-06 121610" src="https://github.com/user-attachments/assets/0d97ba48-4a58-4bc0-a9a4-bb1668e2c7ff" />






Example (trimmed) output:

```
$ ls -la
drwxr-xr-x 2 bandit3 bandit3 4096 Oct 6 12:00 inhere

$ cd inhere
$ ls -la
-rw-r--r-- 1 bandit3 bandit3 33 Oct 6 12:00 ...Hiding-From-You

$ cat ./...Hiding-From-You
<2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ>
```

### Result

Found a hidden file inside a subdirectory and retrieved the password for `bandit4`.

### Notes / Lessons

* `ls -a` (or `ls -la`) shows hidden files beginning with `.`.
* Hidden files may contain secrets—administrators should audit such locations and set appropriate permissions.

---

# Level 4 → Level 5 (method used to find the password file by data type)
> A hint given is that we're looking for a human-readable file in the inhere directory
### Goal

Find the file containing the `bandit5` password when the file is not obviously named — instead the hint points to >

### Methodology

Use `file` command to give us the data type of files in the directory.

### Commands & Output

```bash
# ssh into level 4
ssh bandit4@bandit.labs.overthewire.org -p 2220

# cd into the 'inhere' directory
```cd inhere 

#use the command file ./* to print the data types 
Example (trimmed) output:

```
$ file ./*
/-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
$ cat ./-file07
<4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw>
```

### Result

Located the file by size and read the password for the next level.

### Notes / Lessons

* `file` is a powerful command that givws insight on the data types of files
* Defensive note: Do not leave sensitive files with easily discoverable properties (e.g., unique size) or accessib>

---

## Artifacts (what I uploaded to this repo)

* `docs/screenshots/` — (placeholders) terminal screenshots for each level showing commands and password output.
* `docs/bandit-0-4.md` — this writeup (text).
* `scripts/` — (future) small helper scripts to automate reconnaissance (e.g., parse `ls` outputs); not included h>

---

## MITRE ATT&CK Mapping (conceptual)

* **Tactic:** Initial Access / Discovery
* **Techniques:** T1086 (Command & scripting interpreter — using shell), T1590 (Gather victim host information / d>
  This mapping is conceptual—these exercises are primarily about discovery and enumeration techniques used by both>

---

## Lessons Learned & Next Steps

* Enumeration and understanding how to reference files with odd names (leading `-`, spaces, hidden dotfiles) are f>
* Next steps: automate common enumeration (`nmap` + parsing), practice PortSwigger labs (XSS/SQLi), and produce cl>
* On the resume: link this repo and include a short bullet like: “Completed OverTheWire Bandit levels 0–4 — docume>

---

## Ethics & Responsible Use

All commands and techniques documented above were executed on the OverTheWire training servers (authorized, legal,>

