# Open-Source Intelligence (Project Capstone) for Security Blue Team

---

## Overview
> **Goal**  Given the Twitter handle — `@sp1ritfyre` — Goal is to find a verified real-world profile and answer the following questions:

 1. What is the hacker's first name?
 2. What is the hacker's last name?
 3. What is the hacker's age?
 4. What country does the hacker live in?
 5. What are some of the hacker's interests? (choose 5)
 6. What company does the hacker work for? 
 7. What is the hacker's position within the company?
 8. What is the full url of the website owned by the hacker?
 9. List any full URLs of websites not owned, but used by the hacker (Blogs only)
 10. What email address has been used by the hacker?

> **Tools used**: Private-mac browser · Google · base64 decoder · hex string decoder.


---

## How I Approached This Step-by-Step:

### 1️⃣  Searched the profile on X
https://twitter.com/sp1ritfyre 
*Use a Private / Incognito window.* The bio contains the suspicious string: cmVkaHVudC5uZXQK.xyz

![Twitter bio](images/im1.png)

---

### 2️⃣  Decoded the Base64 blob
Pasted **`cmVkaHVudC5uZXQK`** into an online Base64 decoder (e.g. <https://base64decode.org>).  
The output is: redhunt.net

![Base64 decode](images/im2.png)

---

### 3️⃣  Google-dorked the handle
Google searched: "sp1ritfyre"

The first result shown is **sp1ritfyre.blogspot.com** on the results page. Clicked and got this: 
![Google dork](images/im3.png)

---

### 4️⃣  Decoded the hex string 
Extracted the long hex value from the blog’s page beside the word location → pasted it into an online Hex-to-Text tool (e.g. <https://www.duplichecker.com/hex-to-text.php>).  
It revealed to be: https://sammiewoodsec.blogspot.com

![Hex decode pt1](images/im4.png)

![Hex decode pt2](images/im5.png)
---

### 5️⃣  Harvested identity details
Clicked the **View my complete profile** page on **sammiewoodsec.blogspot.com** and found the personal data.  
![About page](images/im6.png)

![Info page](images/im7.png)
---

### Extracted Identity - Answer to The Questions: 
| Field | Value |
|-------|-------|
| **Name** | Sammie Woods |
| **Age** | 23 |
| **Country** | United Kingdom |
| **Employer** | PhilmanSecurityInc |
| **Position** | Junior Penetration Tester |
| **E-mail** | d1ved33p@gmail.com |
| **Interests** | Security · Photography · Gaming · Malware Analysis · Camping |

---

## Outcome
By putting together a single Base64 decode, one Google dork, and a hex decode, I linked `@sp1ritfyre` → **redhunt.net** → **sammiewoodsec.blogspot.com**, confirming the owner as **Sammie Woods** with complete profile details required for the Managed Service Provider (MSP) breach.

