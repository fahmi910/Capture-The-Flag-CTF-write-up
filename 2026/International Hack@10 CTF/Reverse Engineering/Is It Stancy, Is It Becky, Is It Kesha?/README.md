# 🎤 Is It Stancy, Is It Becky, Is It Kesha? — Writeup (Reverse Engineering)

---

## Flag
```
HACK10{wa00d6d88epd0z1x6gro@rediffmail.com}
```

---

## Challenge Overview

We are given a .NET executable:

```
registries.exe
```

### Objective:
- Identify the correct email address that satisfies the program logic  
- Submit it in the format:

```
HACK10{email@domain.com}
```

---

## Approach

Instead of executing the binary (which may be unsafe), we perform:

> Static analysis using a .NET decompiler

---

## Reverse Engineering

The binary was analyzed using:

- **dnSpy**

### Target Class:

```
Program
```

---

## Key Functions Identified

- `CheckEmailExists(string email)`  
- `HashEmail(string email)`  

These functions define the validation logic.

---

## ⚠️ Core Logic Analysis

### 1. Email Validation via API

```csharp
await client.GetStringAsync("https://appsecmy.com/d22646ad92dfaa334f9fa1c3579b4801.txt");
```

### Observation:
- The program fetches a remote `.txt` file  
- This file contains a list of valid email addresses  
- The input email **must exist in this list**

---

### 2. Hash Verification

```csharp
if (hash == "0d103375d4f99df6bc92a931aa8f48b1")
```

### Observation:
- The program computes:

```
MD5(email)
```

- The correct email must match the given hash  

---

## Attack Strategy

To solve the challenge:

1. Download the email list:
```
https://appsecmy.com/d22646ad92dfaa334f9fa1c3579b4801.txt
```

2. Iterate through each email  
3. Compute MD5 hash  
4. Compare with target hash:

```
0d103375d4f99df6bc92a931aa8f48b1
```

---

## Script Used

```python
import hashlib

target = "0d103375d4f99df6bc92a931aa8f48b1"

with open("emails.txt") as f:
    for line in f:
        email = line.strip().lower()
        if hashlib.md5(email.encode()).hexdigest() == target:
            print("FOUND:", email)
```

---

## Solution

Matching email:

```
wa00d6d88epd0z1x6gro@rediffmail.com
```

---

## Final Result

```
HACK10{wa00d6d88epd0z1x6gro@rediffmail.com}
```

---

## Key Takeaways

- Static analysis avoids executing potentially unsafe binaries  
- Hardcoded hashes can be reversed using brute-force techniques  
- Combining external data sources with hashing logic is a common pattern  
- Automation scripts significantly speed up analysis  

---

## Tools Used

- dnSpy (.NET reverse engineering)  
- Python (`hashlib`)  
- Basic scripting and automation  

---

## Skills Developed

- .NET reverse engineering  
- Static code analysis  
- Hash cracking / matching  
- Automation using Python  
- Logical problem decomposition  

---

⭐ *This challenge demonstrates how reverse engineering combined with simple scripting can efficiently break validation logic.*
