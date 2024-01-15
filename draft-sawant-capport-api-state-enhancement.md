---
title: "Captive Portal API State Structure Enhancement"
abbrev: "CAPPORT State Enhancement"
category: info

docname: draft-sawant-capport-api-state-enhancement-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time Area (art)"
workgroup: "Captive Portal Interaction"
keyword:
 - capport api state
 - token
 - internet access
 - notification
venue:
  group: "Captive Portal Interaction (capport)"
  type: "Working Group"
  mail: captive-portals@ietf.org
  github: "pbsawant/draft-psawant-capport-enhancement"
  latest: https://github.com/pbsawant/draft-psawant-capport-enhancement/blob/main/draft-sawant-capport-api-state-enhancement.md

author:
 -
    fullname: Paresh Sawant
    organization: Apple Inc.
    email: paresh_sawant@apple.com

normative:

informative:


--- abstract

This document specifies new keys in Captive Portal API State data structure. The purpose of these new keys is to allow clients to fetch more useful information from the Captive Portal system.

--- Status of This Memo


--- middle

# Introduction

As described in RFC 8908, the Captive Portal API data structure is specified in JavaScript Object Notation (JSON) [RFC8259]. Requests and responses for the Captive Portal API use the "application/captive+json" media type.
The structure uses key "captive" to indicate the state of captivity. However, many captive networks do not grant full Internet access to the clients even after satisfying the conditions presented by web portal specified by "user-portal-url" key. It means captive=false does not always imply a full Internet access. This document specifies a new key that specifically indicates state of full Internet access.
The original specification specifies key "user-portal-url" to convey the web portal URL to the client. Although in most cases client devices are capable of presenting the web portal to the user, there are types of devices that are not built to support the user interaction with the web portal. This document speccifies a new key that allows client to perform the authentication without user interaction.
On expiry of time specified by key "seconds-remaining" or exceeding the data consumption specified by "bytes-remaining" clients query the API state. This document specifies a new key that allows the API server to notify an update in the APi state to the client.  

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# API State Structure Enahancement

Key: auth-url
Type: string
Description: This is an optional key. It provides the URL of the Authentication Server that must be accessed over TLS. Authentication Server authenticates clients using the HTTP authentication framework specified in RFC 7235 and the server MUST NOT require user interaction on the client device. The client SHOULD have a credential to perform the authentication without user interaction. 
Reference: RFC7235

Key: internet-access
Type: boolean
Description: This is an optional key. It Iindicates whether the client has full Internet access.  This key is relevant only when the “captive” key is set to false. Its default value is true.

Key: persist-api-server-connection
Type: boolean
Description: This is an optional key. It indicates whether client can establish a persistent connection with Captive Portal API Server. The purpose of the persistent connection is to allow the API server to send an updated API state to the client. With the help of this key the client can avoid polling the API state on expiry of seconds-remaining or bytes-remaining. This key is relevant only when the "captive" key is set to false. Default value is false.

Key: allow-domains
Type: string
Description: This is an optional key. It indicates a list of domains that the client is authorized to access. The value of this key is specified in comma separated value (CSV) format. The purpose of this key is to allow the client to handle the network connection errors for domains that are not specified by this key. This key is relevant only when the "captive" key is set to false. Default value is false.


# Security Considerations

This document recommends security consideration specified in {{section 7 of RFC 8908}}

# Privacy Considerations

This document recommends privacy consideration specified in {{section 7.1 of RFC 8908}}


# IANA Considerations

IANA is requested to add the keys specified in "API State Structure Enahancement" section.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
