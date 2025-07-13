## Key points:
1. this is a reflected xss
2. the vulnerability is in search functionality
3. user input get reflected directly inside DOM 
## What I need to do:
- find the xss vulnerability in the search
- craft a payload to call alert() function
## How I solved it:
![home](pics/vulnElement1.png)
1. passed the string `duckky` in the search
2. noticed in the url search parameter `?search=duckky` reflected into the `h1` tag in DOM
3. passed the string `<script>alert(1)</script>` and it returned the alert box proving xss vulnerability
## Why this works:
![dom](pics/dom1.png)
In this lab the website directly copies the search parameter and puts it inside `h1` tag. There is no encoding of the angle braces(`< >`) or forward slash(`/`). for this reason after the response the `h1` tag looks like this `<h1><script>alert(1)</script></h1>` which executes the JavaScript code.
![proof of concept](pics/poc1.png)

## Payload:
`<script>alert(1)</script>`