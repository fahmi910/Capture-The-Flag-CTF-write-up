# 🎰 Hakari Domain 2 — Writeup (Crypto)

---

## Flag
```
hack10{22a41542ef29a7f60a4b7b46fcab6174}
```

---

## Challenge Overview

This challenge combines **two major vulnerabilities**:

1. Predictable RNG using Python’s `random` (MT19937)  
2. A weakened AES implementation with **SubBytes disabled**

### Objective:
- Predict RNG outputs to reach the **jackpot phase**
- Exploit the broken AES encryption
- Recover the hidden **secret**
- Submit the secret to obtain the flag  

---

## Source Code Analysis

### RNG Initialization

```python
seed = os.urandom(8)
random.seed(seed)
target = random.getrandbits(32)
```

- Uses Python’s `random` module (MT19937)
- Seed is only **8 bytes**
- Output is **32-bit per guess**

---

### Information Leak

```python
print(f"Wrong. The number was {target}.")
```

Each incorrect guess leaks a **raw RNG output**.

---

## Vulnerability #1 — Recoverable MT19937 Seed

### Key Constraints:
- Only **250 attempts allowed**
- Full state recovery (624 outputs) is not possible

---

### Exploitation Strategy

Instead of recovering full state:

> Recover the **seed directly** (small-seed attack)

Python’s `random.seed(bytes)`:
- Converts seed deterministically into internal state
- Small seed (8 bytes) → feasible to recover

---

## Stage 1: Seed Recovery

### Steps:
1. Send incorrect guesses (e.g., always `0`)
2. Collect ~234 outputs:
   ```
   Wrong. The number was X
   ```
3. Apply MT19937 seed-recovery technique (bytes-based seeding)
4. Validate candidates by replaying outputs

---

### Result

Recovered seed:

```
65c6361c0d0c1156
```

Now RNG becomes fully predictable.

---

## Stage 2: Trigger Jackpot

Using recovered RNG:

```python
predicted = rng.getrandbits(32)
```

Send correct predictions:

```
Correct → streak 1  
Correct → streak 2  
Correct → streak 3  
```

🎉 Jackpot unlocked.

---

## Vulnerability #2 — Broken AES (No SubBytes)

Critical modification:

```python
cipher._sub_bytes = huh
```

### Impact:

- SubBytes = **only non-linear step in AES**
- Removing it makes AES **fully linear**

---

### Resulting Model:

\[
E(x) = Mx \oplus b
\]

Where:
- `M` = linear transformation matrix  
- `b` = constant vector  

---

## Stage 3: Oracle Exploitation

After jackpot:
- We gain access to an **encryption oracle**
- Given:
  ```
  secret_enc = E(secret)
  ```

Goal:
👉 Recover `secret`

---

## Stage 4: Recover AES Components

### Step 1: Recover Constant `b`

Encrypt zero:

```python
b = E(0)
```

---

### Step 2: Recover Matrix `M`

For each bit (128 total):

Construct basis vector:
```
p = 00...01...00
```

Query:
```
E(p)
```

Compute:
```
column = E(p) ⊕ b
```

This reconstructs all columns of matrix `M`.

---

### Step 3: Solve Linear System

We know:

\[
secret\_enc = M \cdot secret \oplus b
\]

Rearrange:

\[
M \cdot secret = secret\_enc \oplus b
\]

Solve using:
- Gaussian elimination
- Over GF(2)

---

### Step 4: Recover Secret

```
secret = 22a41542ef29a7f60a4b7b46fcab6174
```

---

## Stage 5: Retrieve Flag

Submit:

```python
encrypt(secret)
```

Condition triggered:

```python
if plaintext == secret:
    print(flag)
```

---

## Final Result

```
hack10{22a41542ef29a7f60a4b7b46fcab6174}
```

---

## Key Takeaways

- MT19937 is insecure when seeded with small entropy  
- Information leakage enables seed recovery  
- Removing non-linearity breaks cryptographic security  
- AES without SubBytes becomes a linear system  
- Linear algebra can fully break encryption  

---

## Tools Used

- Python  
- MT19937 seed recovery techniques  
- Custom scripting for prediction  
- Linear algebra (Gaussian elimination over GF(2))  
- Netcat (`nc`)  

---

## Skills Developed

- RNG seed recovery (MT19937)  
- Cryptographic vulnerability analysis  
- Understanding AES internals  
- Linear algebra in cryptography  
- Oracle-based attacks  
- Multi-stage exploit chaining  

---

⭐ *This challenge demonstrates how combining weak randomness and broken cryptographic design leads to complete system compromise.*
