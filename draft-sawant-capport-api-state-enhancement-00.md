---
title: Captive Portal API State Structure Enhancement
abbrev: CAPPORT API State Enhancement
category: info
docname: draft-sawant-capport-api-state-enhancement-00
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

Table 1 shows the new key that can be optionally included in the
top-level of the JSON structure returned by the API server.

~~~ aasvg
+=================+=========+==================================+
| Key             | Type    | Description                      |
+=================+=========+==================================+
| authentication- | string  | It provides the URL of the       |
| server-url      |         | Authentication Server that MUST  |
|                 |         | be accessed over TLS.            |
|                 |         | Authentication Server            |
|                 |         | authenticates clients using the  |
|                 |         | HTTP authentication framework    |
|                 |         | specified in [RFC9110].          |
|                 |         | The server MUST NOT require      |
|                 |         | user interaction on the client   |
|                 |         | device. The client MUST have a   |
|                 |         | credential to perform the        |
|                 |         | authentication without user      |
|                 |         | interaction.                     |
+-----------------+---------+----------------------------------+
~~~
 
# Security Considerations

This document recommends security considerations specified in
{{Section 7 of !RFC8908}}.

# Privacy Considerations

This document recommends privacy consideration specified in
{{Section 7.1 of !RFC8908}}.

# IANA Considerations

IANA is requested to add the new key specified in Table 1.

--- back
