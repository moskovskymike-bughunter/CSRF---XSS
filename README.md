# CSRF---XSS
CTF challenge from pwn.college
The server have a vulnerable entry point http://challenge.server/localhost/ephemeral with message ppost redirecting to itself. So it's a good place to inject the xss sxript.
Server runs with /challenge/server& and index.html with payload runs on python3 -m http.server 1337. After the bot login the server and leaves cookies and visit attacker server.
