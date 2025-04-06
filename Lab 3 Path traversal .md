# 🛡️ Lab Write-up: File Path Traversal – Traversal Sequences Stripped with Superfluous URL-decode

---

## 🧪 Lab Overview

This PortSwigger lab demonstrates a **file path traversal vulnerability** due to improper input handling in the image filename parameter.

Although the application blocks standard traversal patterns like `../`, it performs **URL-decoding after filtering**, allowing attackers to bypass protections using **double-encoded payloads**.

> 🎯 **Objective:** Retrieve the contents of the sensitive file `/etc/passwd`.

---

## 🧰 Tools Used

- 🌐 Web Browser  
- 🛠️ [Burp Suite](https://portswigger.net/burp) (Community or Pro Edition)  
- 🧪 PortSwigger Web Security Academy Lab Environment  

---

## 🧠 Understanding the Vulnerability

The application attempts to block dangerous file paths like:

- `../`  
- `%2e%2e%2f`

However, it makes a critical mistake: it **decodes the input *after* filtering**, which can reintroduce malicious path traversal sequences that had initially been hidden through **double URL encoding**.

### 🔓 The Bypass: Double Encoding

To bypass the filter:

- `/` → `%2f`  
- `%2f` → `%252f`  
- So, `../` becomes: `..%252f`  

🟢 The double-encoded sequence isn't blocked and is later decoded into a valid `../` at runtime, allowing access to restricted files.

---

## 🚀 Exploitation Steps

### 1️⃣ Access the Application

- Open the lab URL.  
- Click on a product image.  
- Note the request pattern:
GET /image?filename=product1.jpg

yaml
Copy
Edit

---

### 2️⃣ Intercept the Request via Burp Suite

- Turn **Intercept ON** in Burp.  
- Click on the image again.  
- Capture the image request:
/image?filename=product1.jpg

yaml
Copy
Edit
- **Send it to Repeater** for modification.

---

### 3️⃣ Craft and Insert the Exploit Payload

Use this **double-encoded traversal payload**:
GET /image?filename=..%252f..%252f..%252fetc/passwd

yaml
Copy
Edit

#### 📌 Explanation:
- `..%252f` → `..%2f` → `../` after decoding
- Reconstructed path: `../../../etc/passwd`

---

### 4️⃣ Send the Payload and View the Results

- Send the modified request in Repeater.  
- ✅ If successful, the response will contain:
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin ...

yaml
Copy
Edit

🎉 **Lab Solved!**
