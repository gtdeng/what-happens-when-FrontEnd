This repo attempts to answer the question: **What happens when you type google.com into your browser and press enter?** from a Front End Developer's point of view and is inspired by [Alex's What-Happens-When Repo.](https://github.com/alex/what-happens-when)


## Navigation Timing Overview
![Navigation Timing Overview](timing-overview.png) [1]


## PRECHECKS
- Browser prompts for unload of current page
- Browser unloads current page
- Browser checks whether it is a search term or a URL
- Browser converts non-ASCII Unicode characters
- Browser redirects to the requested URL


## APP CACHE / SERVICE WORKERS
- Browser checks for cached data in browser storage (1.Local Storage 2.Session Storage 3.Cookies Storage 4.Cache Storage 5.Application Cache)
- Service Workers intercepts requests, and routes browser requests to a local cache if present
- If there's cached data, it will return the cached data stored in your sessionStorage or localStorage


## DNS RESOLVER
- Browser asks for the IP address that corresponds to URL you entered from the DNS Cache in Chrome, your local hosts file and then your DNS on your router and then failing that, your ISP's caching DNS server
- DNS Resolver finds the shortest endpoint's IP address and sends it back to the Browser


## TCP CONNECTION
- Browser initiates a connection to that IP using port 80 or 443 (depending on whether you are browsing via HTTP port 80 or the secured HTTPS port 443)
- Browser opens a Socket via TCP
- Browser initiates a TLS 3 way handshake checking for certificates to ensure an encrypted channel


## ACTUAL REQUEST
- Browser checks it's Cookies Storage (aka cookies jar)
-- If found and not expired, Browser attaches a cookie with each subsequent request
- Browser sends the actual Request for the resource


## BACKEND RESPONSE
- Backend Server processes request
- Backend sends 301 (redirects) if your requested url is missing 'www' or 'https'
- The HTTP/web server (apache/nginx) processes the URL you entered
- Backend sends 404 (not found) or continues it's resource generation
- Backend code retries/generates the HTML and execute queries from the DB
- Backend sends back a 200 (ok) RESPONSE to the web-server
-- Backend normally sends HTML page to the browser via the HTTP channel
-- Backend could send a ServiceWorker.js to establish a ServiceWorker, which improves page performance
-- Backend could also perform "server-side rendering", rending the entire page on the backend and sending the associated html, css, js, state as an 'html string' which is then parsed by the client


## BROWSER PROCESSING
- Browser receives HTML page and executes the following steps in the Critical Rendering Path:
  1. domLoading
  2. domInteractive
  3. domContentLoaded
  4. domComplete
  5. onLoad


### 1. DOMLOADING (domLoading)
- Browser receives the first bits of the server response
- Browser fires off a "domLoading" event
- Since this is the first milliseconds of the actual browser processes, it is normally used as the start time of measuring how our pages are being rendered by a browser
- Browser "parses" the HTML from top to bottom to construct the "DOM"


### 2. DOMINTERACTIVE (domInteractive)
- Browser fires off a "domInteractive" event and marks Document Interactive, since the DOM can be manipulated
- At this point, the initial HTML document has been completely loaded, parsed and the DOM is ready for edits/manipulation via CSS or JS scripts.
- Browser "synchronously fetches" the page's subresouces: external images, fonts, CSS, and JS
-- "domInteractive" serves as a useful metric to measure a website's speed from the user's perspective (Netflix uses this metric)


### 3. DOMCONTENTLOADED (domContentLoaded)
- At this stage, all the DOM Contents (html, css, js) will be loaded
- Browser fires off a "domContentLoadedStart" event
- Browser parses the CSS files from the top down and the bottom styles overwrite the top rules
- Browser builds "CSSOM"
- Browser combines the CSSOM and the DOM into a "render tree"
- Browser parses traditional blocking scripts. see JS Scripts Run [3.5]
- Browser computes the "layouts" of each visible element
- Browser computes the exact position and size of each node in the render tree
- Browser "paints" the individual nodes to the screen and renders the pixels to the screen "first paint"
- Browser must have downloaded all CSS before firing off "domContentLoadedEnd"
- Browser fires off a "domContentLoadedEnd" event
- jQuery's callback inside $.ready() are called
- The page is finally visible in the viewport!!!


### 3.5. JS SCRIPTS RUNS
- If you are using JS to make a cross-domain XHR call and the browser realizes that:
-- IF it is a simple GET request, it will proceed and make the actual XHR call
-- IF it is a complicated POST/DELETE etc request, it will go thru it's preflight checklist before making the OPTIONS call to the server with all the custom details. IF the server responds with the appropriate Access-Control-* header, the Browser will then make the actual XHR request.
- Backend parses and responds to the request


### 4. DOMCOMPLETE (domComplete)
- Browser loads images, videos and executes any JS scripts listening for the DOMContentLoadedEnd event
- Browser fires off a "domCompelete" event-handlers
- Browser continues to download images and other non-blocking assets and repaints as necessary
- Browser paints and replaces the white <img> placesholders with actual images
- Browser's loading spinner stops spinning


### 5. ONLOAD (onLoad)
- Browser has received ALL resources that the page requires has been download and parsed (tho not ran)
- Browser fires off a "Loaded" event and marks Document Loaded
- All HTML resources (fonts, images, html contents) have been fully loaded and any dom content changes after this point will result in a visible "flash"
- Browser executes JS code waiting for the onLoad event
- Browser parses scripts in 'deferred' mode
- Service Worker gets installed
- Cookies and other content are set in the Local Storage
- Analytics starts monitoring user interactions


___
This is all licensed under the terms of the Creative Commons Zero license.
[1] https://www.w3.org/TR/navigation-timing/
