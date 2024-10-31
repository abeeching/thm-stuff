# Web Application Basics

## [Task 1] Introduction

In this room, we'll learn about some key elements of web applications, which can be useful if you're looking to build web apps and secure them. This includes what web apps are, how URLs work, and how HTTP works.

## [Task 2] Web Application Overview

A web application is an intricate, layered program that you can explore with just a web browser. The front-end is the surface of the web app, the parts that you interact with via the web browser. This includes technologies such as HTML, CSS, and JavaScript.
- Hypertext Markup Language, HTML: Instructions/code that tells a browser what to display and how to display it.
- Cascading Style Sheets, CSS: Describes the standard appearance, including certain colors, text types, and layouts.
- JavaScript, JS: Enables more complex activity in the browser, letting websites choose/decide what to display.

The backend includes everything you don't see on a web page, but are important for the web application to work.
- Databases are used to store, modify, and retrieve information, including visitor preferences and more.
- Infrastructure components include web servers, app servers, storage, networking devices, and other software.
- A Web Application Firewall, WAF, is optional but can filter out dangerous requests to protect the web server.

In brief: Frontend components focus on the things we can see and interact with in the _web browser_, whereas backend components are the things beneath the web application that allow it to function, including web servers.

**[Task 2, Question 1] Which component on a computer is responsible for hosting and delivering content for web applications?** - web server

**[Task 2, Question 2] Which tool is used to access and interact with web applications?** - web browser

**[Task 2, Question 3] Which component acts as a protective layer, filtering incoming traffic to block malicious attacks, and ensuring the security of the web application?** - web application firewall

## [Task 3] Uniform Resource Locator

A Uniform Resource Locator (URL) is a web address that lets you access all kinds of online content, including webpages, videos, photos, and other media. This lets your browser know where you want to go. A URL is made up of several components that help the browser along in this regard. Key components include:
- The scheme: The protocol used to access the website, commonly `http://` and `https://` for websites on HTTP and HTTPS. As discussed in previous rooms, HTTPS is more secure and preferred to HTTP.
- The user: Some URLs may include a user's login details - their username - for sites that require authentication. This happens when dealing with URLs that need credentials to access certain resources. This is not seen very often anymore due to security concerns, but it would look like this: `http://(USER)@(HOST)...`
- The host or domain name: The most important part of the URL, indicating what website you are accessing. Domain names are unique and must be registered. Adversaries use domain names that look similar to real ones in what's known as _typosquatting_.
- The port: It can be used to direct the browser towards the right service on the web server. Commonly we use 80 and 443 for HTTP/HTTPS, and we don't need to specify anything, though we can if we have to -- `http://(USER)@(HOST):(PORT)`
- The path: Follows a `/` in the URL, indicating the specific file or page that you'd like to access. In securing web apps you need to make sure that only certain users can access sensitive data.
- The query string: A part of the URL that starts with a question mark `?`, often used for search terms and form inputs. Needs to be handled with care, since they can lead to injection attacks.
- Fragments: Start with `#` symbols to point to a specific section of a webpage, e.g. to specific headings and tables. Users can modify this, so you need to watch for issues like injection attacks.

**[Task 3, Question 1] Which protocol provides encrypted communication to ensure secure data transmission between a web browser and a web server?** - HTTPS

**[Task 3, Question 2] What term describes the practice of registering domain names that are misspelt variations of popular websites to exploit user er rors and potentially engage in fraudulent activities?** - typosquatting

**[Task 3, Question 3] What part of a URL is used to pass additional information, such as search terms or form inputs, to the web server?** - query string

## [Task 4] HTTP Messages

HTTP messages are packets of data exchanged between the client and the server and are important for understanding how web apps work - they show how user requests and serer responses are communicated. Things like the headers, method, URL, and status codes are all key to making web apps work. There are two types of HTTP messages:
- HTTP requests, which are sent by the user to trigger actions in an application, and
- HTTP responses, which are sent by the server in response to the user's requests

There is a specific format that each message follows to help facilitate smooth communications:
- Start line: The introduction, telling you what kind of message is being sent - a request or a response. This also gives information abuot how the message has to be handled.
- The headers: Key-value pairs that provide information about the HTTP message, giving instructions to both the client and server handling the request or response. Covers security, content types, and more.
- Empty lines: These are dividers that separate the header from the body - this shows where the actual message begins. Without an empty line to separate the header and message, the message might get messed up or misinterpreted.
- The body: This is where data is actually stored, including data the user wants to send to the server or where the server puts the content the user requested.

Understanding HTTP messages is key to understanding how web apps communicate, and if you know how they work you can diagnose issues in web app communication and security.

**[Task 4, Question 1] Which HTTP message is returned to the web server after processing a client's request?** - HTTP response

**[Task 4, Question 2] What follows the headers in an HTTP message?** - Empty line

## [Task 5] HTTP Request: Request Line and Methods

HTTP requests are what users send to web servers to interact with a web app and make stuff happen. This is the first point of contact between the user and web server.

The request/start line in an HTTP request is the first part of a request and tells the server what kind of request it's dealing with. It looks like this: `(METHOD) /(PATH) HTTP/(VERSION)`.
- HTTP methods: Tell the server what action the user wants to perform on the resource in the URL path. Common methods include:
  - `GET`: Fetch data from the server without making changes. You should only let the user fetch data they're allowed to see.
  - `POST`: Send data to the server to create and update something. You should ensure the input coming in is validated.
  - `PUT`: Replace and update something on the server. Ensure the user is authorized to do so.
  - `DELETE`: Remove something from the server. Ensure the user is authorized to do so.
  - `PATCH`: Update part of a resource and make small changes. Validate the data to avoid inconsistencies.
  - `HEAD`: Retrieve only headers and not the full content. Good for checking metadata only.
  - `OPTIONS`: Tells you what methods are available for a resource. May want to disable for security reasons.
  - `TRACE`: Similar to OPTIONS, showing what methods are allowed for debugging. Often disabled for security reasons.
  - `CONNECT`: Used to create a secure connection, like for HTTPS. Not as common, but needd for encrypted comms.
- URL Path: Tells the server where to find the resource the user is asking for. This is often manipulated to exploit vulnerabilities. Ensure the URL path is validated to prevent unauthorized access and sanitized to prevent injection. Conduct privacy and risk assessments to ensure that sensitive data that can be accessed via URLs is secured.
- Version: The protcol version used for communication, most commonly being one of the following:
  - HTTP/0.9: First version from 1991, only supported GETs
  - HTTP/1.0: Added headers and support for other types of content, 1996
  - HTTP/1.1: Persistent connections, chunked transfer encoding, better caching. Still used today despite being made in 1997
  - HTTP/2: Multiplexing, header compression, prioritization for faster performance, 2015
  - HTTP/3: Built on HTTP/2 but uses QUIC protocol for better speed and secure connectivity, 2022
 
For a while you will see many systems still using HTTP/1.1, since that's got a lot of support and is used in many pre-existing setups. Performance and security improves as more users switch to later HTTP versions.

**[Task 5, Question 1] Which HTTP protocol version became widely adopted and remains the most commonly used version for web communication, introducing features like persistent connections and chunked transfer encoding?** - HTTP/1.1

**[Task 5, Question 2] Which HTTP request method describes the communication options for the target resource, allowing clients to determine which HTTP methods are supported by the web server?** - OPTIONS

**[Task 5, Question 3] In an HTTP request, which component specifies the specific resource or endpoint on the web server that the client is requesting, typically appearing after the domain name in the URL?** - URL path

## [Task 6] HTTP Request: Headers and Body

Request headers convey additional information to the web server about the request, including:
- The Host, which specifies the name of the web server the request was meant for
- The User-Agent, which gives information about the web browser the request comes from
- The Referer, which indicates the URL the request came from
- The Cookie, which stores information the web server wanted the web browser to hang on to
- The Content-Type, which stores the type or format of data in the request.

In HTTP POST and PUT requests, where data is sent to the web server as opposed to being requested from the server, the data can be found in the HTTP Request body. This data can be formatted in a few ways:
- URL Encoded, `application/x-www-form-urlencoded`: This is a format where data is structured in key-value pairs, where `key=value`. Multiple pairs are separated by an `&` symbol, and special characters are percent-encoded. Examples include `username=admin&password=admin`.
- Form Data, `multipart/form-data`: Each block gets separated by a boundary string, which is the defined header of the request itself. This can be used to send binary data, e.g. uploading files and images. If you see data divided by `----WebKitFormBoundary...` or something, you've got form data (you can also check the Content-Type, but...)
- JSON, `application/json`: Data is sent with the JavaScript Object Notation (JSON) structure, with data formatted in `name : value` pairs. Multiple pairs are separated by commas, and all is contained in curly braces. Includes stuff like `"username" : "admin", ...`
- XML, `application/xml`: Data is structured into tags with openings and closings. Tags are nested within each other as needed to store information. It'll be stuff like `<username>admin</username>`.

**[Task 6, Question 1] Which HTTP request header specifies the domain name of the web server to which the request is being sent?** - Host

**[Task 6, Question 2] What is the default content type for form submissions in an HTTP request where the data is encoded as `key=value` pairs in a query string format?** - `application/x-www-form-urlencoded`

**[Task 6, Question 3] Which part of an HTTP request contains additional information like host, user agent, and content type, guiding how the web server should process the request?** - request headers

## [Task 7] HTTP Response: Status Line and Status Codes

Web servers will send back responses when they receive HTTP requests. These let you know that the request went through or that something has gone wrong. The first line in an HTTP response is the Status Line, telling you three things:
- The HTTP version in use. This was covered in Task 5.
- The status code of the request, a three-digit number showing the outcome
- A reason phrase, a short message explaining the status code

Status codes fall into five categories:
- 100-109: Informational responses, indicating the server has received part of the request and is on standby, waiting for the rest.
- 200-299: Success responses, indicating all has gone well. The server was able to process the request and send data back.
- 300-399: Redirect responses, indicating the resource has been moved elsewhere. Usually you get the new URL.
- 400-499: Client error responses, indicating something was wrong with the request (e.g., URL, missing info)
- 500-599: Server error responses, indicating the server primarily had an issue processing things. Not the client's fault.

Common status codes include
- 100: Continue - The server is ready for the rest of the request to come through.
- 200: OK - The request was successful, and the server is shooting the requested resource back.
- 301: Moved Permanently - The requested resource has been moevd to a new URL that you need to use from now on.
- 404: Not Found - The server could not find the resource in the given URL. Check the address.
- 500: Internal Server Error - Something went wrong on the server's end and it couldn't process the request.

**[Task 7, Question 1] What part of an HTTP response provides the HTTP version, status code, and a brief explanation of the response's outcome?** - Status line

**[Task 7, Question 2] What category of HTTP response codes indicates that the web server encountered an internal issue or is unable to fulfill the client's request?** - Server error responses

**[Task 7, Question 3] Which HTTP status code indicates that the requested resource could not be found on the web server?** - 404

## [Task 8] HTTP Response: Headers and Body

HTTP response headers are usually sent back with HTTP responses, and these too contain key-value pairs. These provide important information about the response and tells the client how to handle it. There are several headers that are needed for the HTTP response to work correctly; this includes
- The Date, showing the exact date and time when the response was generated.
- The Content-Type, showing what kind of content the client is getting (e.g. HTML, JSON, etc.) and the character set (UTF-8).
- The Server, which indicates what kind of server sofwtare is handling the request. This is good for debugging, and it's also good for attackers looking to get information about the server... folks tend to remove or obscure this.

Other responses headers that you'll commonly see include:
- `Set-Cookie`, which sends cookies from the server to the client, which is then stored by the client for future requests. Cookies should be set with the `HttpOnly` flag so they can't be accessed by JS and with the `Secure` lag so they're only sent over HTTPS.
- `Cache-Control`, which tells the client how long it can cache the response before checking with the server again. If used with `no-cache`, this can help protect sensitive info from being cached.
- `Location`, which is used in redirect responses, telling the client where to go if the resource has been moved. If users are able to modify this, validate and sanitize this, as this can result in vulnerabilities otherwise.

The HTTP response body contains the actual data - HTML, JSON, images, and more. This is all sent back to the client. To prevent injection attacks such as cross-site scripting (XSS), sanitize and escape the data before including it in the response.

**[Task 8, Question 1] Which HTTP response header can reveal information about the web server's software and version, potentially exposing it to security risks if not removed?** - Server

**[Task 8, Question 2] Which flag should be added to cookies in the `Set-Cookie` HTTP response header to ensure they are only transmitted over HTTPS, protecting them from being exposed during unencypted transmissions?** - Secure

**[Task 8, Question 3] Which flag should be added to cookies in the `Set-Cookie` HTTP response header to prevent them from being accessed with JavaScript, thereby enhancing security against XSS attacks?** - HttpOnly

## [Task 9] Security Headers

HTTP Security Headers can be used to improve the security of a website by providing mitigations against common vulnerabilities such as XSS and clickjacking. Websites like `securityheaders.io` can be used to analyze the headers on any website.

The Content Security Policy (CSP) header can be used to guard against XSS. Malicious code may be hosted on a seaprate website or domain and then injected into the vulnerable site. CSP lets administrators say what domains and sources are considered safe. Properties such as `default-src` or `script-src` may be defined and many more, giving administrators the ability to define what's allowed with various amounts of granularity. Note that `self` is a special keyword that reflects the same domain on which the website is hosted.
- Example: `Content-Security-Policy: default-src 'self'; script-src 'self' (CDN_SERVER); style-src 'self'`
  - `default-src` specifies the default policy of `self`, which means only the current site.
  - `script-src` specifies where scripts can be loaded from - the website itself and a CDN server in this case.
  - `style-src` specifies where CSS stylesheets can be loaed from - the website itself in this case
 
The Strict Transport Security (HSTS) header ensures that browsers always connect with HTTPS.
- Example: `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
  - `max-age` gives the time in seconds for this setting before it expires
  - `includeSubDomains` is an optional setting that applies this to all subdomains
  - `preload` is an optional setting that allows the website to be included in preload lists, enforcing HSTS before a user even visits the website for the first time
 
The `X-Content-Type-Options` header instructs browsers not to guess the MIME type of a resource, but only use the Content-Type.
- Example: `X-Content-Type-Options: nosniff` tells a browser not to sniff or guess the MIME type.

The `Referrer-Policy` header controls the amount of information sent to the destination web serer when a user is redirected from the source web server, e.g. upon clicking a hyperlink. This lets an administrator control what information is shared.
- Examples include `Referrer-Policy: no-referrer`, `same-origin`, `strict-origin`, and `strict-origin-when-cross-origin`.
  - `no-referrer`: Disables information being sent about the referrer
  - `same-origin`: Sends only referrer information when the destination is part of the same origin. Useful for when you want referrer information apssed when using hyperlinks within the same site.
  - `strict-origin`: Sends the referrer as the origin only when the protocol stays the same, e.g. when an HTTPS connection goes to another.
  - `strict-origin-when-cross-origin`: SImilar to `strict-origin` except for `same-origin` requests, where it sends the full path in the origin header.

**[Task 9, Question 1] In a Content Security Policy (CSP) configuration, which property can be set to define where scripts can be loaded from?** - `script-src`

**[Task 9, Question 2] When configuring the Strict-Transport-Security (HSTS) header to ensure that all subdomains of a site also use HTTPS, which directive should be included to apply the security policy to both the main domain and its subdomains?** - `includeSubDomains`

**[Task 9, Question 3] Which HTTP header directive is used to prevent browsers from interpreting files as a different MIME type than what is specified by the server, thereby mitigating content type sniffing attacks?** - `nosniff`

## [Task 10] Practical Task: Making HTTP Requests

Let's make some demo web requests with the static site. We'll start by trying to make a GET request to `/api/users`. It's as simple as adding `/api/users` to the end of the URL and setting the request type to GET. Then we just read the output at the bottom for the flag:

![image](https://github.com/user-attachments/assets/41ecf138-3221-42c0-b685-ef40a43f20b2)

**[Task 10, Question 1] Make a GET request to `/api/users`. What is the flag?** - `THM{YOU_HAVE_JUST_FOUND_THE_USER_LIST}`

This next request will be a bit more complicated. We need to send a POST request to `/api/user/2`, and we have to change something. Cilcking the Gear icon next to the Go button lets us set parameters. We'd like to set this user's `country` to `US`, so we'll do this. Make sure to click the Save (floppy disk) icon for the parameter to actually carry through.

![image](https://github.com/user-attachments/assets/0339fffb-9303-4bf1-8642-4e4574d3f7bc)

...and then we'll issue the request. This yields the following:

![image](https://github.com/user-attachments/assets/c268d6f1-1c3e-41ca-8382-345a127ee0e5)

**[Task 10, Question 2] Make a POST request to `/api/user/2` and update the country of Bob from UK to US. What is the flag?** - `THM{YOU_HAVE_MODIFIED_THE_USER_DATA}`

And now we'll delete a user by issuing a DELETE request to `/api/user/1`. Doing so reveals the last flag.

![image](https://github.com/user-attachments/assets/5c4e44ce-99c0-47d9-add3-d2e809012862)

**[Task 10, Question 3] Make a DELETE request to `/api/user/1` to delete the user. What is the flag?** - `THM{YOU_HAVE_JUST_DELETED_A_USER}`

## [Task 11] Conclusion

And that'll do it - we've just learned about how web apps work, what role URLs play, and how HTTP messages are set up. More importantly for cybersecurity, we learned some basic ways to secure HTTP and web app communications.
