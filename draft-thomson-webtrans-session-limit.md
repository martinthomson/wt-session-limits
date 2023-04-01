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
 -
    fullname: Eric Kinnear
    organization: Apple Inc.
    email: ekinnear@apple.com

normative:
  WTH3: I-D.ietf-webtrans-http3
  WTH2: I-D.ietf-webtrans-http2
  QUIC: RFC9000

informative:


--- abstract

Limits to how a WebTransport session uses QUIC resources like streams or data
can help limit the effect that one WebTransport session has on other uses of the
same HTTP/3 connection.  This describes mechanisms for limiting the number of
streams and quantity of data that can be consumed by each WebTransport session.


--- middle

# Introduction

WebTransport in HTTP/3 {{WTH3}} provides applications with all the functionality
of QUIC {{QUIC}} streams.  In the case where a single connection includes a
WebTransport session that needs to coexist with other WebTransport sessions or
HTTP requests, the core draft does not offer any way to place limits on stream
usage.

This document describes an additional layer of session-level flow control that
governs the creation of streams and sets a session-level limit on the amount of
data that can be exchanged in a session.

This document does not define a framework for prioritizing the streams created
for a WebTransport session with other streams.

Note that this document is intended as input for {{WTH3}}. Although it is
possible to define this as an extension to that protocol, integration of this
design is simpler; see {{negotiation}}.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Protocol Definition

This document uses the following flow control capsules defined in {{WTH2}}:

* WT_MAX_DATA ({{Section 5.5 of WTH2}})
* WT_MAX_STREAMS ({{Section 5.7 of WTH2}})
* WT_DATA_BLOCKED ({{Section 5.8 of WTH2}})
* WT_STREAMS_BLOCKED ({{Section 5.10 of WTH2}})

These capsules are unchanged, except that where the WebTransport over HTTP/2
capsules refer to streams that flow over the HTTP/2 stream containing the
entire WebTransport session, these capsules refer to separate limits as
described in subsequent sections.

These capsules use the codepoints allocated in {{WTH2}}.


## Stream Limits

The WT_MAX_STREAMS capsule establishes a limit on the number of streams within a
WebTransport session.  Like the QUIC MAX_STREAMS frame ({{Section 19.11 of
QUIC}}), this capsule has two types that provide separate limits for
unidirectional and bidirectional streams that are initiated by a peer.

The session-level stream limit applies in addition to the QUIC MAX_STREAMS
frame, which provides a connection-level stream limit.  New streams can only be
created within the session if both the stream- and the connection-level limit
permit; see {{Section 4.6 of QUIC}} for details on how QUIC stream limits are
applied.

Unlike the WT_MAX_STREAMS capsule or the QUIC MAX_STREAMS frame, there is no
simple relationship between the value in this frame and stream IDs in QUIC
STREAM frames.  This especially applies if there are other users of streams on
the connection.

The WT_STREAMS_BLOCKED capsule is sent to indicate that an endpoint was unable
to create a stream due to the session-level stream limit.


## Data Limits

The WT_MAX_DATA capsule establishes a limit on the amount of data that can be
sent within a WebTransport session.  This limit counts all data that is sent on
streams of the corresponding type, excluding the stream header (see {{Sections
4.2 and 4.2 of WTH3}}).  The stream header is excluded from this limit so that
this limit does not prevent the sending of information that is essential in
linking new streams to a specific WebTransport session.

Implementing WT_MAX_DATA requires that the QUIC stack provide the WebTransport
implementation with information about the final size of streams; see {{Section
4.5 of QUIC}}.

The WT_DATA_BLOCKED capsule is sent to indicate that an endpoint was unable to
send data due to a limit set by the WT_MAX_DATA capsule.

Because WebTransport over HTTP/3 uses a native QUIC stream for each WebTransport
stream, per-stream data limits are provided by QUIC natively.  The
WT_MAX_STREAM_DATA and WT_STREAM_DATA_BLOCKED capsules are not used and so are
prohibited.  Endpoints MUST treat receipt of a WT_MAX_STREAM_DATA or a
WT_STREAM_DATA_BLOCKED capsule as a session error.


# Negotiation {#negotiation}

If the use of flow control capsules are merged into the main specification
{{WTH3}}, their use will be negotiated along with the use of WebTransport over
HTTP/3.  This is the simplest approach.

Alternatively, if this remains as an optional extension, new HTTP/3 settings
will be needed to negotiate the use of these features.  In the abstract, we
could define settings that carry initial values for the three variables that are
controlled by the session-level flow control capsules defined here.  The
presence of any of those settings would indicate that these limits will be
respected if the capsule is sent.

Both peers need to indicate the setting before these capsules apply.  If only
one peer advertises any of these settings, that might indicate that they are
willing to receive and respect session-level flow control capsules.  However,
such an endpoint cannot know when to start applying the limit.


# Security Considerations

Aside from new exposure to the usual programming errors arising from increased
protocol complexity, it is believed that the introduction of these capabilities
only improves security as it provides better control over endpoint resource
allocation.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
