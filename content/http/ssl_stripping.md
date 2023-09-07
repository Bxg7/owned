---
title: "SSL stripping"
date: 2019-01-10
# geekdocFlatSection: false
# geekdocToc: 6
# geekdocHidden: false
---

https://moxie.org/software/sslstrip/

Sslstrip requires that the connection between the client and the sslstrip instance is done over plain HTTP.  
The connection from sslstrip to the webserver can be either HTTP and HTTPS.  

This means that if the client <u>first requests the site with plain HTTP</u> then sslstrip works. If instead the clients starts already with HTTPS since it either knows that the site is only accessible by HTTPS or if the browser enforces HTTPS because of a HSTS policy (preloaded or from earlier visits) then sslstrip will not work.

Clarification:

* The attacker sits between the victim and a server.
* When the attackers receives an HTTP request from the victim, the attacker sends the request to the server using HTTPS.
* When the attacker receives the response from the server, it strips away the HTTPS links and then forwards the response to the victim using HTTP.  
It can strip all this data because it is the attacker who has initiated the encrypted channel with the webserver, NOT the client. The client is only spoon fed HTTP data.

Sslstrip analyses the response headers and

removes accept-encoding
removes if-modified-since

This is so the client has to make a full request to the server, even if the page hasn't been modified. This header needs to be removed because otherwise there is no complete request to the server and nothing to intercept.

removes cache-control

This is for the same reason as if-modified-since, so the client doesn't try to load the page from a cache and avoid making a complete request to the request to the server.

changes link in Location header to HTTP

![sslstrip](sslstrip.png)
