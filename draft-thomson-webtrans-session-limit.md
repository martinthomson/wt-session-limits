---
title: "Applying Per-Session Limits for WebTransport"
abbrev: "WebTransport Session Limits"
category: info

docname: draft-thomson-webtrans-session-limit-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time"
workgroup: "WebTransport"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "WebTransport"
  type: "Working Group"
  mail: "webtransport@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/webtransport/"
  github: "martinthomson/wt-session-limits"
  latest: "https://martinthomson.github.io/wt-session-limits/draft-thomson-webtrans-session-limit.html"

author:
 -
    fullname: Martin Thomson
    organization: Mozilla
    email: mt@lowentropy.net

normative:

informative:


--- abstract

Limits to how a WebTransport session uses QUIC resources like streams or data, can help limit the effect that one WebTransport session can have on other users of the same HTTP/3 connection.

--- middle

# Introduction

TODO Introduction


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
