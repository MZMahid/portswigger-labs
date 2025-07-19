## Key points:
- this is a dom based cross-site scripting vulnerability
- the vulnerability is in the search functionality

## What I need to do:
- Exploit DOM based XSS to call an `alert()` function

## How I solved it:
1. Fist I entered some random arbitrary string `mmr403` in the search
2. noticed that string is shown in the url `?search=mmr403`
3. Since this is a DOM based XSS I checked the DOM where the js takes the value of search query and pass it to an `img` tag `src` attribute. 
4. To break out of that tag and call alert() I passed a string `"><script>alert(1)</script> //`
![home](pics/vulnElement2.png)

## Why this works:
In this lab the javascript directly pass the search query to the src attribure of the image tag. This does not encode anything so it run as script in DOM. here I first added a `"` to exit the `src` attribure the wrote the actual scripts. at the end the `//` comments out anything tha comes after the the payload.
![dom](pics/dom3.png)

![proof of concept](pics/poc3.png)

## Payload:
`"><script>alert(1)</script> //`