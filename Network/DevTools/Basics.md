## network
Network tab shows everything needed to render the current page. So not just API requests but assets like css, js, images, etc. It also has status column and time column. Why does it
show everything it needs to render the page? cuz **network tab downloads everything needed**, like js files, images, html, etc.

btw theres 2 types of api - xhr/fetch VS preflight.

- xhr = xml http request, which is old way for js to make api request
- fetch = newer way
- preflight = special **OPTIONS** REQUEST sent before the actual API call to check for cross-origin resource sharing (cors)

the type can also have (blocked:other) or (blocked:client) in the Status column, this is NOT an HTTP status code - it's Chrome telling you the request was blocked BEFORE it even reached the server.

(blocked:other) = Browser extension blocked it (ad blocker, privacy tool)
(blocked:client) = Same thing, extension interference
(blocked:csp) = Content Security Policy blocked it
(blocked:mixed-content) = HTTPS page trying to load HTTP resource. So most likely its atlassian fault cuz developer put invalid link.
(blocked:devtools) = You manually blocked it in DevTools so its customer fault.

### wats cors?
its browser security that controls if a website can make a request to another domain 

so the flow is like
1) Browser sends OPTIONS request (preflight) asking: "Can I make a POST/PUT/DELETE with these headers?"
2) Server responds with CORS headers saying: "Yes, you can" or doesn't respond properly
3) If allowed: Browser sends the actual API request (POST/GET/etc.)
4) If blocked: Browser stops and shows CORS error

## console
shows javascript errors, warnings and logs.

particularly
- console.log(), console.error() from code
- JavaScript errors (syntax, runtime errors)
- Warning messages
- Stack traces showing which file/line number caused error

The error info is also provided with the type and message. For e.g.
```
Uncaught TypeError: Cannot read property 'name' of undefined at app.js:42
```
it also shows the file name and the line number and stack trace. You can click on the file name and it will redirect to **sources tab**.

## flow
1) network (tab) downloads everything like app.js, xx.html, etc
2) browser actually executes this js file
3) then its the console tab that shows the execution result

## elements
this shows HTML structure (DOM tree) and the css styles for a selected element.

for example if a button is not clickable and no error in console shows up, should look at elements tab. Maybe etiher pointer-events is none or 
z-index problem or there is another div on top.

z-index = stacking order so the higher the number, the "on top" it is. So the reason y it might not work is cuz
```
Button: z-index: 1
Overlay div: z-index: 10
```

## diff between sources and elements (v impt)
they show both html and css. But **elements shows the live DOM** while sources show original downloaded files. So html and css can change based on js so elements tab should be used
to inspect the **current page state**. But sources just is a read-only view for *source code* and is best for putting debugging breakpoints.

If JavaScript modifies the page (adds elements, changes HTML), Elements tab shows the modified version, but Sources tab shows original file.

## application
User logged out but still appears logged in after refresh. Instead of clearing the entire cache, should look at application tab.

This is cuz this tab stores Cookies, Local Storage, Session Storage and Cache Storage. The auth info could be stored here. like session_id, auth_token, user_token, JSESSIONID in cookie, and same
named variables in local storage.

btw local storage Survives browser restart/close but session storage deletes info when tab is closed.



