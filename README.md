This repo attempts to answer the question: "What happens when you type google.com into your browser and press enter?" from a Front End Developer's point of view. This repo has been inspired by [Alex's What Happens When.](https://github.com/alex/what-happens-when)


![Navigation Timing Overview](timing-overview.png) [1]


### PRECHECKS
--Browser prompts for unload of current page
--Browser unloads current page
--Browser checks whether it is a search term or a URL
--Browser converts non-ASCII Unicode characters
--Browser redirects to the requested URL


### APP CACHE / SERVICE WORKERS
--Browser checks for cached data in browser storage (1.Local Storage 2.Session Storage 3.Cookies Storage 4.Cache Storage 5.Application Cache)
--Service Workers intercepts requests, and routes browser requests to a local cache if present
--If there's cached data, it will return the cached data stored in your sessionStorage or localStorage


### DNS RESOLVER
--Browser asks for the IP address that corresponds to URL you entered from the DNS Cache in Chrome, your local hosts file and then your DNS on your router and then failing that, your ISP's caching DNS server
--DNS Resolver finds the shortest endpoint's IP address and sends it back to the Browser


### TCP CONNECTION
--Browser initiates a connection to that IP using port 80 or 443 (depending on whether you are browsing via HTTP port 80 or the secured HTTPS port 443)
--Browser opens a Socket via TCP
--Browser initiates a TLS 3 way handshake checking for certificates to ensure an encrypted channel


### ACTUAL REQUEST
--Browser checks it's Cookies Storage (aka cookies jar)
---If found and not expired, Browser attaches a cookie with each subsequent request
--Browser sends the actual Request for the resource


### BACKEND RESPONSE
--Backend Server processes request
--Backend sends 301 (redirects) if your requested url is missing 'www' or 'https'
--The HTTP/web server (apache/nginx) processes the URL you entered
--Backend sends 404 (not found) or continues it's resource generation
--Backend code retries/generates the HTML and execute queries from the DB
--Backend sends back a 200 (ok) RESPONSE to the web-server
---Backend normally sends HTML page to the browser via the HTTP channel
---Backend could send a ServiceWorker.js to establish a ServiceWorker, which improves page performance
---Backend could also perform "server-side rendering", rending the entire page on the backend and sending the associated html, css, js, state as an 'html string' which is then parsed by the client


### BROWSER PROCESSING
-Browser receives HTML page and executes the following steps in the Critical Rendering Path:
  1-domLoading
  2-domInteractive
  3-domContentLoaded
  4-domComplete
  5-onLoad




## You see google.com's homepage load with all the background scripts fully loaded after which you jump in joy.


[1] https://www.w3.org/TR/navigation-timing/
