# 🧩 Challenge: Operation Broken Stripe

![CTF](https://img.shields.io/badge/Category-Forensics-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Hard-red)
![Technique](https://img.shields.io/badge/Technique-RAID%20Reconstruction-orange)

---

## Description

Two disk images (`disk_0.bin` and `disk_1.bin`) were recovered from a failed system. The system was confirmed to be using **RAID 0**, but the stripe size is unknown.

The goal is to reconstruct the original data and recover the flag.

---

## Initial Analysis

First, inspect both disk images:

```bash id="raid1"
ls -l disk_0.bin disk_1.bin
file disk_0.bin disk_1.bin
```

Then examine raw bytes:

```bash id="raid2"
od -An -tx1 -v disk_0.bin | head
od -An -tx1 -v disk_1.bin | head
```

---

## Stripe Size Discovery

A PNG file begins with the signature:

```text id="raid3"
89 50 4E 47 0D 0A 1A 0A
```

Observation:

* `disk_0.bin` → `89 50 4E 47`
* `disk_1.bin` → `0D 0A 1A 0A`

This indicates:

* The header is split across disks
* Each disk contributes **4 bytes alternately**

> ✅ Stripe size = **4 bytes**

---

## Reconstruction

To rebuild the original file, we interleave 4-byte chunks from each disk.

### Python Script

```python id="raid4"
from pathlib import Path

d0 = Path("disk_0.bin").read_bytes()
d1 = Path("disk_1.bin").read_bytes()

stripe = 4
out = bytearray()

for i in range(0, max(len(d0), len(d1)), stripe):
    out += d0[i:i+stripe]
    out += d1[i:i+stripe]

Path("raid0.png").write_bytes(out)

print("Reconstruction complete")
```

---

## 🔐 Extracting the Flag

Once reconstructed, extract readable content:

```bash id="raid5"
strings -a raid0.png | grep ictf
```

---

## Final Flag

```text id="raidflag"
ictff8{R41D_0_R3c0v3ry_M4st3r}
```

---

## Tools Used

* 🐍 Python
* 🔍 `strings`
* 🧰 `od`
* 🐧 `file`

---

## Skills Developed

* Understanding RAID 0 striping
* Binary reconstruction techniques
* Identifying file signatures
* Writing scripts for data recovery

---

## Key Takeaways

* RAID 0 splits data across disks without redundancy
* Stripe size is critical for reconstruction
* File signatures help identify correct alignment
* Simple scripting can recover complex data structures

---
