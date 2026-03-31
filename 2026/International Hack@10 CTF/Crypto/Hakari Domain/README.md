# 🎰 Hakari Domain — Writeup (Crypto)

> “You learned Hakari's Domain. Will you hit the jackpot?”

---

## Flag
```
hack10{ab3a61603241b0638804acdc5f905cd4}
```

---

## Challenge Overview

We are given access to a remote service:

```
nc 34.126.187.50 5500
```

### Objective:
- Guess random 32-bit numbers  
- Achieve **3 correct guesses in a row** → unlock jackpot  
- After jackpot → receive RSA encryptions of the flag  

---

## Initial Analysis

At first glance, this appears to be a simple guessing game:

```python
target = random.getrandbits(32)
```

However, a critical behavior changes everything:

```python
print(f"Wrong. The number was {target}.")
```

### Key Observation:
Every incorrect guess **leaks the correct random number**.

This transforms the challenge from guessing into:

> Can we predict the RNG?

---

## Vulnerability #1 — Predictable RNG (MT19937)

Python’s `random` module uses **Mersenne Twister (MT19937)**, which is **not cryptographically secure**.

### Important properties:
- Internal state: **624 values (32-bit each)**
- If enough outputs are known → state can be reconstructed
- Once reconstructed → future outputs are predictable

---

## Exploitation Strategy

### Step 1: Collect RNG Outputs

Instead of trying to win, we intentionally lose:

```
Input: 0
Output: Wrong. The number was X
```

Repeat until **624 outputs** are collected.

---

### Step 2: Reconstruct RNG State

Using tools like:

- `randcrack`

We feed all observed outputs into the predictor:

```python
rc.predict_getrandbits(32)
```

Now the RNG is fully predictable.

---

### Step 3: Trigger Jackpot

We predict the next values and send them:

```
Correct → streak 1  
Correct → streak 2  
Correct → streak 3  
```

🎉 Jackpot unlocked.

---

## RSA Phase

After jackpot, the server returns:

```
n = ...
e = 17
c = ...
```

### Observations:
- Same plaintext (flag)
- Different modulus `n`
- Constant exponent `e = 17`
- No prime reuse

---

## Vulnerability #2 — Håstad’s Broadcast Attack

This setup is vulnerable because:

If the same message is encrypted with:
- Different moduli `n`
- Same small exponent `e`

Then:

\[
c_i = m^e \mod n_i
\]

With enough samples (≥ 17), we can recover:

\[
m^e
\]

Using **Chinese Remainder Theorem (CRT)**.

---

## Exploitation Steps

### Step 4: Collect RSA Samples
After jackpot, repeatedly:
- Predict correct number
- Receive `(n, c)`

Collect at least **17 samples**.

---

### Step 5: Recover Plaintext

Using:

```python
C, N = crt(moduli, ciphertexts)
m = iroot(C, 17)
flag = long_to_bytes(m)
```

---

## Final Result

```
hack10{ab3a61603241b0638804acdc5f905cd4}
```

---

## Key Takeaways

- Non-cryptographic RNG (MT19937) is predictable  
- Information leakage can completely break randomness  
- Small RSA exponent + repeated plaintext = critical weakness  
- Combining multiple vulnerabilities can fully compromise a system  

---

## Tools Used

- Python  
- `randcrack` (MT19937 state recovery)  
- `sympy` / `gmpy2` (CRT & root extraction)  
- Netcat (`nc`)  
- Basic scripting for automation  

---

## Skills Developed

- Cryptographic vulnerability analysis  
- RNG state reconstruction (MT19937)  
- Understanding RSA weaknesses  
- Applying Håstad’s Broadcast Attack  
- Exploit chaining (RNG → RSA)  
- Practical CTF problem-solving  

---

⭐ *This challenge demonstrates how multiple small weaknesses can combine into a full system compromise.*
