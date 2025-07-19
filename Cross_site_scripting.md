
##  1. Reflected XSS into HTML context with nothing encoded
- **Location:** reflected cross site scripting vulnerability in blog search function
- **Payload:** `<script>alert(1)</script>` or `<img src=1 onerror=alert(1)>`
- **Root cause:** server reflects user input without sanitization.





