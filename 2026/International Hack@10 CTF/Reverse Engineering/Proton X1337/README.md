# 📱 Proton X1337 — Writeup (Mobile / Reverse Engineering)

---

## Flag
```
HACK10{j3mpu7_s3r74_0W4SP_C7F}
```

---

## Challenge Overview

We are given an Android APK that appears legitimate, but the description suggests it secretly transmits data to a **Command-and-Control (C2) server**.

### Objective:
- Identify the hidden C2 server  
- Analyze the application behavior  
- Recover the flag  

### Hint:
> No brute force or scanning required  

👉 This indicates a **static analysis challenge**

---

## Approach

Since the target is an APK:

- Focus on **decompilation and code inspection**  
- Avoid dynamic interaction or network scanning  
- Look for suspicious functions and hardcoded endpoints  

---

## Static Analysis

After decompiling the APK, the main logic was found in:

```
com.example.protonx1337.MainActivity
```

### Suspicious Function:

```
backdoorC2
```

This function is responsible for the malicious behavior.

---

## Malware Behavior

The `backdoorC2` function performs the following:

- Collects local data  
- Reads a file:
  ```
  tdata_backup.txt
  ```
- Constructs a JSON payload  
- Sends the data via HTTP POST request  

### Insight:
The reference to Telegram-related files suggests **data exfiltration**.

---

## Step 1: Identify C2 Server

From the decompiled code and extracted strings:

```
https://appsecmy.com/pages/liga-ctf-2026
```

### This is:
👉 The Command-and-Control (C2) endpoint

---

## Step 2: Analyze the Endpoint

Visiting the URL:

- The page appears normal  
- No visible flag in the rendered content  

---

## Step 3: Inspect HTML Source

Using **View Source**:

A hidden flag was found inside an HTML comment.

---

## Decoy Detection

Inside the APK, a fake flag was also found:

```
HACK10{n0t_A_Fl4g}
```

### Observation:
- This is a **decoy**
- Not the correct answer  

---

## Final Result

### C2 Server:
```
https://appsecmy.com/pages/liga-ctf-2026
```

### Flag:
```
HACK10{j3mpu7_s3r74_0W4SP_C7F}
```

---

## Key Takeaways

- APK files can contain hidden malicious logic  
- Static analysis is effective for identifying embedded backdoors  
- Hardcoded URLs often reveal C2 infrastructure  
- Flags may be hidden in web source, not visible UI  
- Decoy flags are commonly used to mislead analysts  

---

## Tools Used

- APK decompilation tools (e.g., JADX, APKTool)  
- String analysis  
- Browser (View Source)  

---

## Skills Developed

- Mobile application reverse engineering  
- Static malware analysis  
- Identifying data exfiltration behavior  
- C2 detection and analysis  
- Recognizing decoy artifacts  

---

⭐ *This challenge demonstrates how malicious mobile applications can hide data exfiltration logic and how static analysis can uncover hidden infrastructure.*
