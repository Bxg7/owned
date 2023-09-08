---
title: "Session Management Intro"
date: "2019-01-01"

# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

* Since HTTP is a stateless protocol (meaning if you send two requests to the same server, it has no idea it came from the same user) the idea of the session came along.
* The session is basically just an object kept in memory by the Web Application so that it can identify each user.
* After the initial request from a user the server will reply back with the session ID and every time a new request comes in from that same user the assigned ID will be in the request.
* In the browser this ID is usually stored as a cookie. So the browser will attach this cookie automatically with each new request. This way the server will actually know that its you.
* IMPORTANT NOTE: the session ID needs to be protected in some way, because it could be stolen by someone that can then hijack your session.
* A cookie can be either a session cookie which will be lost after you close the browser or it can be permanent which will then be written to disk when you close your browser and loaded again when you open the browser.
* Most cookies are used for marketing or tracking.
* The Set-Cookie HTTP response header is used to send cookies from the server to the user agent:

```sh
Set-Cookie: <cookie-name>=<cookie-value>
Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date>
Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<non-zero-digit>
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>
Set-Cookie: <cookie-name>=<cookie-value>; Path=<path-value>
Set-Cookie: <cookie-name>=<cookie-value>; Secure
Set-Cookie: <cookie-name>=<cookie-value>; HttpOnly
Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Strict
Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Lax
```

Multiple directives are also possible, for example:
```sh
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>; Secure; HttpOnly
```
- If the Path value is set to http://www.reddit.com the cookie is also valid for all requests that come from reddit SUBDOMAINS.
- If there is no Domain value set the COOKIE is only valid for the domain that send it.
- HttpOnly is a flag added to cookies that tell the browser not to display the cookie through client-side scripts, so it can not be used by Javascript in any cross site scripting attacks.
- The Secure value is used to determine if the COOKIE should be sent only threw encrypted channels.
- SESSION ID NAME FINGERPRINTING: The session ID names used by the most common web application development frameworks can be easily fingerprinted, such as PHPSESSID (PHP), JSESSIONID (Tomcat/J2EE), CFID & CFTOKEN (ColdFusion), ASP.NET_SessionId (ASP .NET), etc. Therefore, the session ID name can disclose the technologies and programming languages used by the web application. It is recommended to change the default session ID name of the web development framework to a generic name, such as “id”.
- SESSION ID LENGTH: The session ID must be long enough to prevent brute force attacks, where an attacker can go through the whole range of ID values and verify the existence of valid sessions. The session ID length must be at least 128 bits (16 bytes).
