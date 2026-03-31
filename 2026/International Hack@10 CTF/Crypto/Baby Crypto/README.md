# Baby Crypto — Writeup (Crypto)

---

## Flag
```
hack10{a88dacd5fb88dc4973bb3a56fff9be940bb1f1b83c2b82f3f6daa256267c9786f4cdc70255079e3cfaea9956211e615fe78ee9d5a95a832afff2f09b05c39db4}
```

---

## Challenge Overview

The challenge processes the flag in **2-byte chunks** and applies SHA-512 hashing.

```python
cipher = hashlib.sha512(flag[i:i+2]).hexdigest()
cipher = cipher[b:a]
output += random_hex + cipher
```

### Key Idea:
- Each chunk of the flag is hashed independently  
- Only a **substring of the hash** is kept  
- Random hex noise is added before each chunk  

---

## Key Observations

- SHA-512 produces **128 hex characters**
- The retained substring is still **long (≈75–127 chars)**
- Input size is **very small (2 bytes)**
- Random noise is present but **does not resemble valid hash substrings**

---

## Vulnerability

Even though hashing is used, the system is insecure because:

> The input space (2 bytes) is extremely small and fully brute-forceable.

Additionally:
- Large portions of the hash are leaked
- These substrings are long enough to be **uniquely identifiable**

---

## Exploitation Strategy

Instead of reversing SHA-512 (impossible), we:

> Precompute all possible inputs and match them against the output.

---

## Attack Steps

### Step 1: Generate All Possible Inputs

Generate all printable 2-character combinations:

```python
import itertools
import string

pairs = [''.join(p) for p in itertools.product(string.printable, repeat=2)]
```

---

### Step 2: Hash Each Pair

```python
import hashlib

hash_map = {}

for pair in pairs:
    h = hashlib.sha512(pair.encode()).hexdigest()
    hash_map[pair] = h
```

---

### Step 3: Handle Substring Extraction

Since the challenge keeps only part of the hash:

- Try possible substring ranges (e.g. offsets 1–15)
- Extract long substrings from each hash

---

### Step 4: Match Against Output

- Scan the challenge output
- Ignore random noise
- Look for **long matching substrings**

Because the substring is long:
- Matches are **unique and reliable**

---

### Step 5: Reconstruct the Flag

- Record matched pairs and their positions  
- Sort them in order  
- Concatenate all recovered 2-byte chunks  

---

## Result

Recovered flag:

```
hack10{a88dacd5fb88dc4973bb3a56fff9be940bb1f1b83c2b82f3f6daa256267c9786f4cdc70255079e3cfaea9956211e615fe78ee9d5a95a832afff2f09b05c39db4}
```

---

## Key Takeaways

- Hashing does **not guarantee security** if input space is small  
- Partial hash leakage can still uniquely identify inputs  
- Random padding/noise does not prevent pattern matching  
- Precomputation attacks are powerful against weak designs  

---

## Tools Used

- Python  
- `hashlib` (SHA-512)  
- `itertools` (brute-force generation)  
- String processing & pattern matching  

---

## Skills Developed

- Cryptographic attack design  
- Brute-force optimization  
- Understanding hash function properties  
- Pattern matching in noisy data  
- Problem decomposition (chunk-based analysis)  

---

⭐ *This challenge demonstrates how poor design choices can completely undermine strong cryptographic primitives like SHA-512.*
