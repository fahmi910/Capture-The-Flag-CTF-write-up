# 📜 I ACCEPT — Writeup (Misc)

---

## Flag
```
hack10{f1n3_pr1nt_n3v3rr_l13ss}
```

---

## Challenge Overview

The challenge presents a **Terms of Service page** for *BlackVault Secure Cloud* and requires extracting a hidden flag.

### Hint:
> “Nobody actually reads these documents anyway.”

This strongly suggests:
- The flag is hidden within the **terms content**
- Requires inspecting **HTML, CSS, or hidden elements**

---

## Initial Analysis

Upon opening the page:
- No visible flag  
- Content appears normal  

However, the hint implies:

> The flag is hidden in plain sight within the document structure.

---

## Step 1: Inspect Page Source

Using:

```
Ctrl + U (View Source)
```

### Key Finding:

```
Clause 4
Clause 12, 19, and Appendix B shall survive termination.
```

### Insight:
This indicates where to focus:

- Clause 12  
- Clause 19  
- Appendix B  

---

## Step 2: Investigate Clause 19

```html
<span class="invisible-fragment">pr1nt_</span>
```

### Observation:
- Hidden using CSS (same color as background)

### Extracted Value:

```
pr1nt_
```

---

## Step 3: Investigate Clause 12

```
Users must always сonsent to periodic aѕѕessments.
```

### Observation:
- Contains **Cyrillic homoglyphs**
  - `с` (Cyrillic c)  
  - `ѕ` (Cyrillic s)

### Meaning:
- This is not part of the flag  
- It acts as a **hint toward obfuscation techniques**

### Supporting Comment:

```html
<!-- TODO: stop hiding audit notes in CSS -->
```

👉 Reinforces that **CSS hiding is important**

---

## Step 4: Inspect CSS (Critical Step)

Inside `style.css`:

```css
#appendix-b::after {
   content: " hack10{f1n3_";
   font-size: 0;
   opacity: 0;
}
```

### Observation:
- Hidden using:
  - `font-size: 0`
  - `opacity: 0`

### Extracted Value:

```
hack10{f1n3_
```

---

## Step 5: Check Footer / Hidden Elements

From HTML:

```html
<div class="legal-footnote">n3v3rr_l13ss}</div>
```

From CSS:

```css
.legal-footnote {
   display: none;
}
```

### Observation:
- Completely hidden from view

### Extracted Value:

```
n3v3rr_l13ss}
```

---

## Step 6: Combine All Parts

| Source              | Extracted Value         |
|--------------------|------------------------|
| Appendix B (CSS)   | hack10{f1n3_           |
| Clause 19          | pr1nt_                 |
| Footer             | n3v3rr_l13ss}          |

---

## Final Result

```
hack10{f1n3_pr1nt_n3v3rr_l13ss}
```

---

## Key Takeaways

- Important data can be hidden in:
  - HTML source  
  - CSS properties  
  - Hidden DOM elements  

- Common hiding techniques:
  - `display: none`  
  - `opacity: 0`  
  - `font-size: 0`  
  - Invisible text  

- Homoglyphs can be used for obfuscation and misdirection  

- Always inspect:
  - Page source  
  - CSS files  
  - Developer tools  

---

## Tools Used

- Browser Developer Tools  
- View Source (Ctrl + U)  
- Manual inspection of HTML & CSS  

---

## Skills Developed

- Web application analysis  
- Hidden content discovery  
- CSS-based obfuscation detection  
- Source code inspection  
- Attention to detail in web challenges  

---

⭐ *This challenge demonstrates how sensitive information can be hidden in web applications using simple but effective obfuscation techniques.*
