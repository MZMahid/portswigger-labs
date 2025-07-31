
## 1. Reflected XSS into HTML context with nothing encoded
- **Location:** reflected cross site scripting vulnerability in blog search functionality
- **Payload:** `<script>alert(1)</script>` or `<img src=1 onerror=alert(1)>`
- **How The Payload Works:** the payload is injected directly into HTML context
- **Root cause:** server reflects user input without sanitization.

## 2. Stored XSS into HTML context with nothing encoded
- **Location:** Stored cross site scripting vulnerability in comment functionality
- **Payload:** `<script>alert(1)</script>` or `<img src=1 onerror=alert(1)>`
- **How The Payload Works:** the payload is injected directly into HTML context
- **Root cause:** Server stores the user input without sanitization.

## 3. DOM XSS in `document.write` sink using source `location.search`
- **Location:** DOM Cross site scripting vulnerability in the `<img>` tag
- **Payload:** `"><script>alert(1)</script>`
- **How The Payload Works:** the first double quote (") closes the `src` attribute and then the angle bracket (>) closes the `img` tag and the script block executes the malicious commands.
- **Root cause:** The search query directly reflects in the `src` attribute of the `<img>` tag without any sanitization


## 4. DOM XSS in innerHTML sink using source `location.search`
- **Location:** DOM-based cross-site scripting vulnerability in the search blog functionality
- **Payload:** `<img src=1 onerror=alert(1)>`
- **How The Payload Works:** this payload tries to render a non existing image named 1. Which return an error which triggers the onerror function `alert`
- **Root cause:** The code does not sanitize user input from `location.search`.
- **Note:** Here the payload `<script>alert(1)</script>` doesn't work because it was inserted via `innerHTML` property. This is blocked in browser for security reasons. But this payload work because browser does not block eventhandler function in this case.

## 5. DOM XSS in jQuery anchor href attribute sink using `location.search` source
- **Location:** DOM-based cross-site scripting vulnerability in the submit feedback page
- **Payload:** `javascript:alert(document.cookie)` set the value of returnPath query to this payload and 
- **How The Payload Works:** this payload inside `href` attribute executes the `alert(document.cookie)` on click
- **Root cause:** the payload directly injected into an `<a>` tag inside the `href` from `location.search`

## 6. DOM XSS in jQuery selector sink using a hashchange event
- **Location:** DOM-based cross-site scripting vulnerability on the home page
- **Payload(exploit server):** `<iframe src="https://lab_uuid_here.web-security-academy.net/#" onload= "this.src += '<img src=1 onerror=print()>'"></iframe>`
- **How The Payload Works:** `<iframe>` loads the vulnerable lab website in another website where after loading the `onload` even handler triggers that changes the hash in the url and add the payload
- **Root cause:** The reason why it works is its on an older vulnerable version of the jquery. this website uses jquery to jump to the title of blogs by putting its string into the hash of the url. But in this jquery version it creates a tag if it doesnt find the hash. which leads to executing the `img` tag payload.
- **Note:** This will only work when the hash changes. So simply putting the payload as hash and loading it will not executing it. that is why we need to use iframe to first load the base url and then append the payload as hash to trigger the hashchange event.

## 7. Reflected XSS into attribute with angle brackets HTML-encoded
- **Location:** reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded.
- **Payload:** `"autofocus onfocus="alert(1);this.onfocus=null` or `"onmouseover="alert(1)`
- **How The Payload Works:** for this we basically need an evenhandler to trigger and call the fuction of malicious function. here 1st one is gareenteed to trigger and `this.onfocus=null` ensures that in only executes once or it would be infinite loop of alert(). 2nd one only works if the user hover the search form with mouse.
- **Root cause:** even though the angle bracket `<>` is encoded but the double quote `"` is not encoded and the dom injection happens direcly inside another html tag inside the `value` attribute. We can easily break out of that attribute and inject any attribute including eventhandlers.

## 8. Stored XSS into anchor href attribute with double quotes HTML-encoded
- **Location:** stored cross-site scripting vulnerability in the comment functionality
- **Payload:** `javascript:alert(1)` put this paylaod in the website form field of the comment functionality
- **How The Payload Works:** this payload inside `href` attribute executes the `alert(1)` on click
- **Root cause:** Because of how the comment functionality is made the website url put in the comment website feild directly injected inside the `herf` attribute of an anchor tag. which then on click execute the alert() fucntion
- **Note:** Is this possible to make this automated execution without relying on the user clicking on the name like we did in lab 7??

## 9. Reflected XSS into a JavaScript string with angle brackets HTML encoded
- **Location:** reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded
- **Payload:** `'; alert(1); let x = 'i` or `-alert(1)-`
- **How It Works:** for the first payload we end the line with semicolon and then put the alert function, but the final single quote give error and the js code does not execute to prevent this we add `let x='i`.
For the second paylaod the final variable value becomes `'' - alert(1) - ''` and since js is not strictly typed languge this returns the string 'alert(1)'
- **Root cause:** Server reflects data from user inside the asignment of a javascript string.

## 10. DOM XSS in `document.write` sink using source `location.search` inside a select element
- **Location:** DOM-based cross-site scripting vulnerability in the stock checker functionality
- **Payload:** `&storeId=<script>alert(1)</script>` append this in the in the product page url
- **How It Works:** this creates a search parameter with that has vaule a common xss Payload
- **Root cause:** For some reason the website takes the valuse of a parameter that has key `storeId` and put it's value inside a `<option>` tag. which allows scope to inject the `<script>` tag.

## 11. DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded
- **Location:** DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality
- **Payload:** `{{ $on.constructor('alert(1)')() }}`
- **How It Works:** In angular js the browser executes some selected js code inside the `{{ }}`. One of them is the `on()` fucntion. A function's constructor property returns referece to the javascript function builder function. Then we can pass any statement or function call inside as a string which creates a function without any name. the final `()` make it so the newly constructed function get called in the same line. Angular js filter out all functions that can be abused but we can use any function that is in scope and get their constructor property and make another fucntion using it.
- **Root cause:** This lab usese angularjs framework, it can be identified by the `ng-app` property in the body or other element. Even though angular js filters most of the js statements and functions but in older version it overlooked this method of using funtion's constructor property to make new function.  
- **Note:** this video summarize this lab very well, https://www.youtube.com/watch?v=QpQp2JLn6JA. to find out what other function are in execute this in console `angular.element(document.getElementsByClassName('search')).scope()` and everything under prototype can be used.

## 12. Reflected DOM XSS
- **Location:** Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response
- **Payload:** `\"-alert(1)} //`
- **How It Works:** This string returned in the value of a json key called "information". we can broke out of the string with \" and then `-` to stay in the same key and then the alert function, then closing the json object with `}` and then comment out rest with `//`
- **Root cause:** this works because the json key value becomes `{"information" : "" - alert()}` which is also a valid js object but not valid json(JSON does not allow expressions or function calls as values). The server always returns json and json is not a functional object but just a formatted string, we then need to turn it into proper asvascript object. One way is using `JSON.parse()` method, which is safe because it turns the string into JavaScript object which would not allow this payload because JSON cannot have expressions or function calls. another way is using `eval()` which turns the string into javascript object by *executing* the string as javascript which then execute the alert() function for the purpose of assigning its return data to the key and that means calling the function which executes the payload. The main reason is using unsafe method of turning string into object `eval()` instead of proper parsing using `JSON.parse()` method.
- **Note:** JSON is not a functional object. Its just a string formated in a way to make it easy to parse this into js object.

## 13. Stored DOM XSS
- **Location:** stored DOM vulnerability in the blog comment functionality
- **Payload:** `<><h1 autofocus onfocus="alert(1);this.onfocus=null">` or `<>`+{any tag containing appropiate event handler}
- **How It Works:** the payload works because it first autofocus to the h1 element and then when focused trigged the onfocus even handler that executes the alert function the next line inside the event handler is so the event trigger once only, but its optional. This is also used in lab 7.
- **Root cause:** the empty tag before does the trick `<>`. The client js uses `String.prototype.replace()` to encode the angle braces. when passed a string inside this string methode it only replace the first occurance of the pattern. So this string method only encode the first pair of curly brace and ignore the once later thus the 2nd element rendered properly.
- **Note:** There is another vulnerability here. The encoding of the angle bracket should have happened in the server not in frontend.

## 14. Reflected XSS into HTML context with most tags and attributes blocked
- **Location:** Reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.
- **Payload(exploit server):** `<iframe src="https://lab_uuid_here.web-security-academy.net/?search=<body+onresize%3D'print()'><%2Fbody>" onload= "this.style.width='100px'"></iframe>`
- **How It Works:** In this lab most of the tags and attributes are blocked and will return 400 status code but using burp intruder we can find the tags and attribute that is not blocked. there is just `<body>` not blocked and a handful of attributes not blocked. We can use any of them to craft a payload, Here I used onresize and the using an iframe I can render the vulnerable page in another page and resize the iframe to trigger the `print()` function.
- **Root cause:** Not all attributes and tags are blocked or encoded

## 15. Reflected XSS into HTML context with all tags blocked except custom ones
- **Location:** In the search functionality there is a reflected XSS but all the HTML tags are blocked
- **Payload:** `<custom autofocus tabindex=1 onfocus="alert(document.cookie); this.onfocus=null">` But for some reason the lab wants us to make it "automated"(considering its already automated). so we have to use the exploit server and the content there will be `<script> location = "https://0a5300b003e96ff980d4039c00610085.web-security-academy.net/?search=%3Ccustom+autofocus+tabindex%3D1+onfocus%3D%22alert(document.cookie)%3B+this.onfocus%3Dnull%22%3E" </script>`
another payload could be `<custom id = 'x' tabindex=1 onfoucs=alert(1)>` and then in exploid server add `#x` at the end of the base url.
- **How It Works:** In javascript we can make a custom tags and give attributes to them. Here we made the custom tag and game it attribute so that it behaves like a focusable element using `tabindex` and then set it to `autofocus` true. `onfocus` even handler executes the alert() fucntion and the `this.onfocus=null` removes the attribute to stop the loop of constant focus.
- **Root cause:** The website does not sanitize angle brackets and double quotes. Instead it blocks all html tags, but does not block attributes and custom tags.
- **Note:** We cannot use iframe in the exploit server. The reason is in the http respose there is the `X-Frame-Options: SAMEORIGIN`. This tells the server to not laod the iframe if the parent page is not from same origin as the iframe source. That is why we used script tag.


## 16. Reflected XSS with some SVG markup allowed
- **Location:** Reflected Cross Site Scripting vulnerability in the Seach functionality but the WAF is blocking most of the tags and attributes
- **Payload:** `<svg><animatetransform onbegin=alert(1)>`
- **How It Works:** Inside `svg` we have another tag that works only inside svg is `animatetransform` and this animates an svg images. the `onbegin` attribute executes whenever the animatetransform tag is rendered, basically it trigger automatically at the start, so we can pass our proff of concept function alert() inside that.
- **Root cause:** Not all tags and attributes are blocked by WAF(Web Application Firewall). Then again the best approach would be to jsut sanitize inputs like `"` and '<>'
- **Note:** To find which tags and attributes are not blocked by WAF we can use burp intruder.

## 17. Reflected XSS in canonical link tag
- **Location:** Reflected Cross Site Scripting Vulnerability in the home page inside a `link` tag
- **Payload:** the payload is appending this query string with the base url `?'accesskey='X'onclick='alert(1)'`
- **How It Works:** The server side code puts the the query string inside a href in link tag. we can sue `'` to break out put the nessasary attributes. The `accesskey` enables simulating clicking on the element with keyboard, for linux the hotkey for this is `Alt + Shit + <the access keys>` in out case Alt + Shift + X. which trigger the onclick() event and executes the alert funciton
- **Root cause:** The server sanitize the double quote but not the isngle quote.
- **Note:** This exploit may not be possible if not for some weird input manipulation does by the server. where when I input the single quote in the query string inside that vulnerable href it shows `" ' "="`. Because of this the href automatically closes for that leading `"`. I don't know why the server does this. But since the `"` is encoded there was no way we could have broken out of the href attribute without that given `"`.


