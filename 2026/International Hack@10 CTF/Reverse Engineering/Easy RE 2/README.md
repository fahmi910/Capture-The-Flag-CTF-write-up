# 📱 Easy RE 2 — Writeup (Mobile / Reverse Engineering)

---

## Flag
```
hack10{minato_namikaze}
```

---

## Challenge Overview

We are given an Android APK file that appears simple at first glance, but actually contains **hidden logic inside a packed structure**.

### Objective:
- Reverse the APK  
- Uncover hidden payload  
- Extract the flag  

---

## Step 1: Initial Analysis

The APK was decompiled using tools such as:

- `jadx`  
- `apktool`  

### Observations:
- Uses a custom class like:
  ```
  ProxyApplication
  ```
- Code appears minimal / obfuscated  
- No obvious flag logic  

### Insight:
These are strong indicators that the APK is **packed (shell APK)** and contains a hidden payload.

---

## Step 2: Finding the Hidden Payload

Further analysis reveals:

- `classes.dex` contains **extra appended data**
- The application decrypts this data using **AES-GCM**
- The decrypted result is another APK:

```
payload.apk
```

### Execution Flow:

```
Original APK → Loader → Decrypt → Real APK (payload)
```

---

## Step 3: Extract and Analyze Payload

After extracting and rebuilding the payload APK:

- The real logic becomes visible  
- Continue analysis on the decrypted APK  

---

## Step 4: Login Logic (Decoy)

Inside the app:

```java
user != pass
MD5(user) == MD5(pass)
```

### Observation:
- This suggests an MD5 collision scenario  
- However, this is **not required to solve the challenge**

### Insight:
👉 This is a **decoy to mislead analysis**

---

## Step 5: Asset Analysis

Inspect the `assets/` folder:

```
background.bkp
background.txt
```

### Observation:
- These files appear suspicious  
- Referenced in native (JNI) code  
- Used in internal processing  

---

## Step 6: XOR Decryption

From analysis of fallback logic:

- `background.bkp` is **XOR-encrypted**
- Key used:

```
0xEF
```

---

### Decryption Script

```python
data = open("background.bkp", "rb").read()
decoded = bytes([b ^ 0xEF for b in data])

open("output.jpg", "wb").write(decoded)
```

---

## Step 7: Extract Flag

After decryption:

- The output becomes a valid **JPEG image**
- Opening the image reveals the flag  

---

## Final Result

```
hack10{minato_namikaze}
```

---

## Key Takeaways

- Packed APKs may hide real logic in encrypted payloads  
- AES-GCM can be used to protect embedded data  
- Not all visible logic is relevant (decoy traps)  
- Native (JNI) code often contains important logic  
- XOR encryption is commonly used for lightweight obfuscation  

---

## Tools Used

- JADX / APKTool  
- Python (XOR decoding)  
- Static analysis tools  
- Basic file inspection  

---

## Skills Developed

- Mobile application reverse engineering  
- APK unpacking techniques  
- Identifying decoy logic  
- XOR decryption  
- Static analysis of native + Java code  

---

⭐ *This challenge demonstrates how layered obfuscation techniques (packing, encryption, and decoys) can hide the true logic of an application.*
