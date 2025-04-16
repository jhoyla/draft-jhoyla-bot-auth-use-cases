---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "Web Bot Auth use Cases"
abbrev: "web-bot-auth-use-cases"
category: info

docname: draft-jhoyla-bot-auth-use-cases-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: AREA
workgroup: WG Working Group
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: https://github.com/jhoyla/draft-jhoyla-bot-auth-use-cases

author:
 -
    fullname: Jonathan Hoyland
    organization: Cloudflare
    email: jhoyland@cloudflare.com
 -
    fullname: Scott Hendrickson
    organization: Google
    email: scott@shendrickson.com

normative:

informative:


--- abstract

TODO Abstract

This work originated from a discussion that occured at an IETF 122 Side Meeting on Authentication Bot Traffic on the web.


--- middle

# Introduction

The Web is increasingly accessed not just by human users operating browsers, but also by automated, non-human clients. These range from traditional web crawlers to AI agent systems

Currently, authenticating the operator of these non-human clients often relies on IP address checking, reverse DNS, and inspecting the `User-Agent` request header. These methods are known to be brittle (e.g. IP addresses rotating), easily spoofed (e.g. User Agents), and can negatively impact security and network operations (due to IP address sharing or rotation).

Any alternative authentication solution must operate within the context of the existing Web infrastructure, primarily designed for human interaction via web browsers. Solutions must be layered onto protocols like HTTP and TLS without breaking existing sites or causing detrimental user experiences (e.g., unexpected authentication prompts for humans).

This document is designed to serve as an informational starting point for potential future work in this area.

# 2. Scope

The document is specifically focused on authenticating **non-human clients** on the **human-oriented Web**. Note this may include automated systems acting on behalf of a human user.

This intentionally excludes:

* Authentication for APIs, although it is acknowledged that APIs are often used in composing modern Web sites.
* Authorisation methods such as OAUTH, which authorise a client to access specific resources on a site.

# 3. Use Case

Enable sites to reliably identify known, well-behaved crawlers used for purposes like search engine indexing, URL scanning services, web archiving, LLM training, and LLM inference. This allows sites or their subcomponents (e.g. captcha providers) to grant them different access levels or rate limits compared to unidentified traffic. This is currently implemented today through serveral common practices:

As mentioned in Section 1, common practices today include:

* **IP Address Allow/Block Lists:** Maintaining lists of IP addresses or CIDR ranges associated with known bots. This is fragile due to dynamic IP allocation (e.g. on Clouds), a lack of consistent IP list discovery mechanism, stale IP lists, and shared IPs on CGNATs.
* **User-Agent String Inspection:** Checking the `User-Agent` request header for strings declared by known bots. This is easily copied and offers no cryptographic assurance.

These methods lack cryptographic robustness, are often inaccurate, do not offer an owner discovery mechanism, and can lead to both misclassifying human traffic as non-human, and traffic getting incorrectly classified.

# 4. Candidate Mechanisms

## 4.1. HTTP Signatures

Using HTTP Signatures {{?RFC9421}} (or a similar JWT approach) allows a client to sign parts of an HTTP request using an asymmetric key pair, providing authenticity and integrity assurance at the application layer. Key considerations include:

* **Replay Attacks:** Signatures could potentially be replayed across different hosts or contexts. Signing components that bind the signature to the specific request context (e.g., the `Host` header, a timestamp, a nonce) is required for mitigation.
* **Key Management:** Similar to mTLS, a mechanism is needed for discovering, provisioning, and managing the keys used for signing.

## 4.2. Mutual TLS (mTLS)

Using TLS client certificates (mTLS) {{?RFC8446}} for authentication provides cryptographic authentication tied to the TLS layer. Key challenges include:

* **User Experience:** Browsers often present confusing or intrusive certificate selection dialogs to users if mTLS is requested unconditionally. draft-jhoyla-req-mtls-flag was presented as a potential way to mitigate this by allowing servers to signal *optional* client certificate authentication support, potentially avoiding issues for users without (or clients that don't support) certificates. Support for this flag will be needed at all TLS Servers and Clients using this method to positively authorize an interaction.
* **Deployment:** Provisioning, managing, and revoking client certificates at scale can be complex, and must be carefully considered by the client owner.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
