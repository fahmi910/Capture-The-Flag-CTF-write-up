# 📄 Dear Hiring Manager — Writeup (Forensics / Malware Analysis)

---

## Flag
```
hack10{M4l1ci0s_PDF}
```

---

## Challenge Overview

The objective of this challenge was to analyze a suspicious PDF file and determine its malicious behavior.

The task required identifying:
- how the payload is triggered
- how the embedded code is obfuscated
- how to recover the final flag

---

## Initial Analysis

From the raw PDF content, a suspicious `/OpenAction` entry was identified.

### Key Observation:
The PDF contains an action that executes automatically when the document is opened.

This is a strong indicator of potentially malicious behavior, since `/OpenAction` is commonly abused in weaponized PDF files.

---

## Malicious Behavior

Inside the action, the following JavaScript was found:

```javascript
vara = ["B0PCd", "0edrK", " 1i+m"];
varb = ["VBeX", "U8:", "ddd$"];
eval(atob(vara.join("") + varb.join("")));
```

### What it does:
1. Splits an encoded string into multiple arrays
2. Joins the arrays into one encoded payload
3. Decodes the payload using `atob()`
4. Executes the decoded result using `eval()`

---

## Why This Is Malicious

This behavior is suspicious because:

- `/OpenAction` allows automatic execution
- `eval()` executes dynamically generated code
- `atob()` is used to hide the real payload
- Splitting strings into arrays adds obfuscation

This combination is a common malware technique used to hide malicious logic inside PDF JavaScript.

---

## Step 1: Rebuild the Encoded Payload

From the arrays:

```javascript
vara = ["B0PCd", "0edrK", " 1i+m"];
varb = ["VBeX", "U8:", "ddd$"];
```

After joining:

```text
"B0PCd0edrK 1i+mVBeXU8:ddd$"
```

Remove the inserted space:

```text
B0PCd0edrK1i+mVBeXU8:ddd$
```

---

## Step 2: Identify the Encoding

The resulting string is not valid Base64.

However, its structure is consistent with **ASCII85 (Base85)** encoding.

This suggests that the JavaScript intentionally uses misleading decoding logic or partial obfuscation to make analysis harder.

---

## Step 3: Decode the Payload

Decoding the payload as ASCII85 reveals:

```text
hack10{M4l1ci0s_PDF}
```

---

## Final Result

```
hack10{M4l1ci0s_PDF}
```

---

## Key Takeaways

- PDF files can contain embedded JavaScript
- `/OpenAction` may trigger code automatically on document open
- Obfuscation can involve string splitting and misleading encoding
- `eval()` is a high-risk function commonly seen in malicious scripts
- Static analysis is essential when inspecting suspicious document files

---

## Tools Used

- Raw PDF content inspection
- Manual string reconstruction
- ASCII85 decoding
- Basic JavaScript analysis

---

## Skills Developed

- PDF malware analysis
- Static analysis of embedded scripts
- Obfuscation detection
- Payload reconstruction
- Encoding identification and decoding

---

⭐ *This challenge demonstrates how malicious PDFs can hide executable logic through JavaScript obfuscation and encoded payloads.*
