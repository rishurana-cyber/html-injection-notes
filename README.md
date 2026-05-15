# HTML Injection — Complete Research Notes

> A comprehensive reference on HTML Injection vulnerabilities for bug bounty hunters and web application penetration testers.

**Author:** Rishu Rana | [@Rishu_2867](https://twitter.com/Rishu_2867) | [GitHub](https://github.com/rishurana-cyber)  
**Platforms:** HackerOne • Bugcrowd • YesWeHack

---

## Table of Contents

- [What is HTML Injection](#what-is-html-injection)
- [How It Works](#how-it-works)
- [Types](#types)
- [Difference from XSS](#difference-from-xss)
- [Where to Find It](#where-to-find-it)
- [Testing Methodology](#testing-methodology)
- [Payloads](payloads/)
- [Bypass Techniques](payloads/bypasses.md)
- [Impact & Severity](#impact--severity)
- [Prevention](#prevention)
- [Resources](#resources)

---

## What is HTML Injection

HTML Injection is a vulnerability where an attacker can inject arbitrary HTML code into a web page that is then **rendered by the victim's browser as real markup**.

It occurs when a web application:
1. Takes user input
2. Reflects it back in the HTTP response
3. Fails to properly encode or sanitize it

**Simple Example:**

```
URL: https://target.com/search?q=<h1>Injected</h1>

Response renders: <h1>Injected</h1> as a real heading
```

---

## How It Works

```
Normal flow:
User inputs "hello" → Server reflects "hello" → Browser shows: hello

Vulnerable flow:
User inputs "<b>hello</b>" → Server reflects "<b>hello</b>" → Browser renders: hello (bold)

Attack flow:
Attacker inputs fake login form HTML → Server reflects it → Victim sees fake login form
```

---

## Types

### 1. Reflected HTML Injection
- Input is reflected **immediately** in the response
- Only affects the user who sends the crafted request
- Usually delivered via malicious URLs
- Severity: **P4/Low** (without phishing scenario) → **P3/Medium** (with phishing PoC)

### 2. Stored HTML Injection
- Injected HTML is **saved in the database**
- Served to every user who visits the page
- No need to trick victims into clicking a link
- Severity: **P3/Medium** → **P2/High**

### 3. DOM-based HTML Injection
- Injection happens **client-side via JavaScript**
- The server never sees the payload
- JavaScript reads attacker-controlled input and writes it to the DOM unsafely
- Severity: Depends on context

---

## Difference from XSS

| Property | HTML Injection | XSS (Cross-Site Scripting) |
|---|---|---|
| Injects | HTML markup | JavaScript code |
| Execution | No script execution | Script executes in browser |
| Impact | Visual/structural changes | Cookie theft, session hijack, keylogging |
| Severity | Lower (P3-P4) | Higher (P1-P2) |
| Relationship | Often a stepping stone to XSS | Escalation of HTML injection |

> **Key insight:** If you can inject `<b>test</b>` and it renders bold, always try `<script>alert(1)</script>` and `<img src=x onerror=alert(1)>` next — HTML injection is often the door to XSS.

---

## Where to Find It

Look for **any input that is reflected back** in the page:

- URL parameters → `?name=`, `?search=`, `?q=`, `?msg=`, `?error=`
- Form fields → search bars, contact forms, profile/bio fields
- HTTP headers → `User-Agent`, `Referer`, `X-Forwarded-For`
- Error messages that echo user input
- Comment/feedback sections
- Registration fields (username, display name)
- Email confirmation pages ("Welcome, USERNAME!")
- 404 pages that reflect the invalid URL

---

## Testing Methodology

### Step 1 — Find Injection Points
Identify all parameters that reflect user input in the response.

### Step 2 — Send Basic Payload
```html
<b>htmlitest</b>
```
Check if "htmlitest" appears **bold** in the browser.

### Step 3 — Verify in Source
Right-click → View Page Source → Search for your payload.

| Finding | Meaning |
|---|---|
| `<b>htmlitest</b>` in source | Vulnerable — tag is unencoded |
| `&lt;b&gt;htmlitest&lt;/b&gt;` in source | Sanitized — not vulnerable |

### Step 4 — Check Content-Type Header
```
Content-Type: text/html  → browser will render HTML (vulnerable)
Content-Type: text/plain → browser won't render HTML (not vulnerable)
```

### Step 5 — Escalate Impact
If basic tags render, inject a fake login form to demonstrate real-world impact.

### Step 6 — Document
- Screenshot of payload in request (Burp Suite)
- Screenshot of rendered output in browser
- Clear attack scenario

---

## Impact & Severity

| Attack Scenario | Severity |
|---|---|
| `<b>test</b>` renders bold, no scenario | P4 / Informational |
| Fake login form injected (reflected) | P3 / Medium |
| Redirect to phishing site | P3 / Medium |
| Fullscreen iframe overlay | P3 / Medium |
| `<base>` tag hijacks all links | P3 / Medium |
| Stored fake login form (all users affected) | P2 / High |
| Escalated to XSS via `onerror` | P2 / High |

---

## Prevention

- **HTML encode all output** — convert `<` to `&lt;`, `>` to `&gt;`
- Use templating engines that **auto-escape by default** (React, Angular, Jinja2)
- Apply a strict **Content Security Policy (CSP)**
- Validate input on the **server side**, not just client side
- Use `textContent` instead of `innerHTML` in JavaScript

---

## Files in This Repo

```
html-injection-notes/
├── README.md                    ← You are here
├── payloads/
│   ├── basic.md                 ← Basic confirmation payloads
│   ├── advanced.md              ← Advanced attack payloads
│   └── bypasses.md              ← WAF and filter bypass techniques
├── methodology/
│   └── testing-checklist.md    ← Step-by-step testing checklist
└── resources.md                 ← References and learning links
```

---

## Resources

See [resources.md](resources.md) for a curated list of references.

---

*These notes are for educational purposes and authorized security testing only. Always obtain proper permission before testing.*
