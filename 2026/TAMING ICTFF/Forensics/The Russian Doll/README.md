# 🧩 Challenge: The Russian Doll

![CTF](https://img.shields.io/badge/Category-Forensics-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)
![Technique](https://img.shields.io/badge/Technique-File%20Carving-red)

---

## Description

We are given a binary file (`evidence.bin`) that appears to contain random data. The challenge hints that a file is hidden inside it.

---

## Initial Analysis

Start by identifying the file type and searching for readable content:

```bash id="rd1"
file evidence.bin
strings -a evidence.bin | head
```

Since the challenge name suggests **nested data ("Russian Doll")**, it is likely that another file is embedded inside.

---

## Identifying Embedded File

ZIP files have a known signature:

```text id="rd2"
PK\x03\x04
```

Search for this signature:

```bash id="rd3"
grep -abo $'PK\x03\x04' evidence.bin
```

---

## Result

The ZIP signature was found at:

```
Offset: 2125
```

This confirms:

* ✅ A ZIP file is embedded inside the binary

---

## Exploitation (File Carving)

Instead of extracting the entire file, we directly carve the embedded content.

From analysis:

* Data start offset = **2163**
* File size = **35 bytes**

### Extraction Command

```bash id="rd4"
dd if=evidence.bin bs=1 skip=2163 count=35 status=none > flag.txt
```

---

## Final Flag

```text id="rdflag"
ictff8{binwalk_is_your_best_friend}
```

---

## Tools Used

* 🐧 `file`
* 🔍 `strings`
* 🔎 `grep`
* 🧰 `dd`

---

## Skills Developed

* File carving techniques
* Identifying file signatures (magic bytes)
* Binary analysis
* Extracting embedded data manually

---

## Key Takeaways

* Many file formats have **unique signatures (magic bytes)**
* Embedded files are common in forensic challenges
* Tools like `dd` allow precise byte-level extraction
* Always search for known headers when dealing with unknown binaries

---
