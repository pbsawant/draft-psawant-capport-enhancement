---
title: Captive Portal API State Structure Enhancement
abbrev: CAPPORT API State Enhancement
category: info
docname: draft-sawant-capport-api-state-enhancement-01
submissiontype: IETF

ipr: trust200902
keyword:
 - capport api state
 - token
 - internet access
 - notification

pi: [toc, sortrefs, symrefs]

author:
 -
    fullname: Paresh Sawant
    organization: Apple Inc.
    email: paresh_sawant@apple.com

--- abstract

This document specifies a new key in Captive Portal API State data
structure. The purpose of the new key is to allow clients to
perform the client authentication without user interaction.

--- middle

# Introduction {#introduction}

As described in {{!RFC8908}}, the Captive Portal API data structure is
specified in JavaScript Object Notation (JSON) {{!RFC8259}}. Requests
and responses for the Captive Portal API use the
"application/captive+json" media type. The original specification
specifies key "user-portal-url" to convey the web portal URL to the
client. Although in most cases client devices are capable of
presenting the web portal to the user, there are types of devices
that are not built to support the user interaction with the web
portal. This document specifies a new key that allows client to
perform the authentication without user interaction.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# API State Structure Enhancement

{{new-key}} shows the new key that can be optionally included in the
top-level of the JSON structure returned by the API server.

| Key             | Type        | Description                  |
|:----------------|:------------|:-----------------------------|
| client-authentication-url | string | Provides the URL of the authentication server that MUST be accessed over TLS with which the client is authenticated without user interaction. Authentication Server authenticates clients using the HTTP authentication framework specified in {{!RFC9110}}. The server MUST NOT require user interaction on the client device. The client MUST have a credential to perform the authentication without user interaction. |
{: #new-key title="Table 1"}

# Use Cases

* Devices that don't support user interaction or web interface,
  can join captive networks using "client-authentication-url"
  key. For example, IoT (Internet of Things) devices or speakers
  can join captive networks using the credentials provisioned to
  them.
  
* Use of "PrivateToken" HTTP authentication scheme can attest that
  the client behind the user agent is likely not a bot attempting to
  perform some form of automated attack such as credential stuffing.
  
* User experience improvements by not requiring user's interaction
  with the Captive Portal system every time client device connects
  the captive network. For example, many Captive Portal systems
  require users to accept the same terms of service or solve CAPTCHA
  or watch some commercial on every connection to captive network.
  
# Example Interaction

Upon discovering the URI of the API server, a client connected to a
captive network will query the API server to retrieve information
about its captive state and conditions to escape captivity.  In this
example, the client discovered the URI "https://example.org/captive-
portal/api/X54PD39JV" using one of the mechanisms defined in
{{!RFC8910}}.

To request the Captive Portal JSON content, a client sends an
HTTP GET request:

~~~ aasvg
GET /captive-portal/api/X54PD39JV HTTP/1.1
Host: example.org
Accept: application/captive+json
~~~

The server then responds with the JSON content for that client:

~~~ aasvg
HTTP/1.1 200 OK
Cache-Control: private
Date: Mon, 19 Aug 2024 05:07:35 GMT
Content-Type: application/captive+json

{
    "captive": true,
    "client-authentication-url": "https://server.example.org/auth"
}
~~~

Upon receiving this information, the client will use it to start
an authentication session with the server (as specified by "client-
authentication-url" key) to enable access to the external network.
The client sends following HTTP request to begin the authentication:

~~~ aasvg
GET /auth HTTP/1.1
Host: server.example.org
~~~

Upon receiving the HTTP request, the server selects appropriate
authentication scheme to authenticate the client. This example
shows "Bearer" authentication scheme defined in {{!RFC6750}}.
{{Section 16.4.1 of ?RFC9110}} specifies the list of authentication
schemes. The server sends HTTP response message with 401
(Unauthorized) status code along with WWW-Authenticate header: 

~~~ aasvg
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="example"
~~~

In response to the received challenge, the client sends an access
token in the "Authorization" request header using "Bearer"
authentication scheme:

~~~ aasvg
GET /auth HTTP/1.1
Host: server.example.org
Authorization: Bearer mF_9.B5f-4.1JqM
~~~

If the access token is found valid, the server sends a response to
the client. An example of such response is:

~~~ aasvg
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache
{
    "access_token":"mF_9.B5f-4.1JqM",
    "token_type":"Bearer",
    "expires_in":3600,
    "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA"
}
~~~

On a successful HTTP authentication, the client SHOULD query the
API server again to verify that it is no longer captive.

When the client requests the Captive Portal JSON content after
gaining external network access, the server responds with updated
JSON content:

~~~ aasvg
HTTP/1.1 200 OK
Cache-Control: private
Date: Mon, 19 Aug 2024 05:08:13 GMT
Content-Type: application/captive+json

{
    "captive": false,
    "venue-info-url": "https://flight.example.com/entertainment",
    "seconds-remaining": 326,
    "can-extend-session": true
}
~~~

# Security Considerations

This document recommends security considerations specified in
{{Section 7 of !RFC8908}}.

# Privacy Considerations

This document recommends privacy consideration specified in
{{Section 7.1 of !RFC8908}}.

# IANA Considerations

IANA is requested to add the new key specified in {{new-key}}.

--- back
