# Resources & References

Curated list of references for HTML Injection and related web security topics.

---

## Learning Platforms

| Resource | URL | Topic |
|---|---|---|
| PortSwigger Web Academy | https://portswigger.net/web-security | XSS, HTML Injection, all web vulns |
| HackTricks | https://book.hacktricks.xyz | Comprehensive hacking reference |
| OWASP | https://owasp.org/www-community/attacks/xss | XSS & Injection theory |
| TryHackMe | https://tryhackme.com | Hands-on labs |

---

## Payload References

| Resource | URL |
|---|---|
| PayloadsAllTheThings | https://github.com/swisskyrepo/PayloadsAllTheThings |
| XSS Cheat Sheet (PortSwigger) | https://portswigger.net/web-security/cross-site-scripting/cheat-sheet |
| HTML5 Security Cheatsheet | https://html5sec.org |

---

## Bug Bounty Platforms

| Platform | URL |
|---|---|
| HackerOne | https://hackerone.com |
| Bugcrowd | https://bugcrowd.com |
| YesWeHack | https://yeswehack.com |
| Intigriti | https://intigriti.com |

---

## Tools

| Tool | Purpose |
|---|---|
| Burp Suite | HTTP interception, testing, fuzzing |
| webhook.site | Free receiver for PoC form submissions |
| Burp Collaborator | Out-of-band detection |
| ffuf | Parameter fuzzing |
| ParamSpider | Discover reflected parameters |

---

## Related Vulnerability Classes

Once you understand HTML Injection, study these next:

| Vulnerability | Why It's Related |
|---|---|
| XSS (Cross-Site Scripting) | HTML injection that executes JavaScript |
| CSRF | Often combined with stored HTML injection |
| Content Security Policy (CSP) | Main defense against HTML/JS injection |
| Template Injection (SSTI) | Server-side version of injection |
| Dangling Markup | Advanced HTML injection technique |

---

## Recommended Reading Order

1. HTML Injection (this repo)
2. XSS — Reflected, Stored, DOM
3. Content Security Policy (CSP) bypass
4. CSRF
5. SSRF
6. IDOR

---

*Built by [Rishu Rana](https://github.com/rishurana-cyber) — documenting the bug bounty journey publicly.*
