---
title: "QUIC New Server Preferred Address"
category: std
docname: draft-munizaga-quic-new-preferred-address-latest

ipr: trust200902
area: "Transport"
workgroup: "QUIC"
keyword: Internet-Draft
venue:
  group: "QUIC"
  type: "Working Group"
  mail: "quic@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/quic/"
  github: "MarcoPolo/new-preferred-address"
  latest: "https://marcopolo.github.io/new-preferred-address/draft-munizaga-quic-new-preferred-address.html"

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    fullname: Marco Munizaga
    organization: Ethereum Foundation
    email: marco@marcopolo.io
 -
    fullname: Marten Seemann
    organization: Smallstep
    email: martenseemann@gmail.com

normative:

  QUIC-TRANSPORT:
    title: "QUIC: A UDP-Based Multiplexed and Secure Transport"
    date: 2021-05
    seriesinfo:
      RFC: 9000
      DOI: 10.17487/RFC9000
    author:
      -
        ins: J. Iyengar
        name: Jana Iyengar
        org: Fastly
        role: editor
      -
        ins: M. Thomson
        name: Martin Thomson
        org: Mozilla
        role: editor


informative:

--- abstract

This document specifies an extension to QUIC to allow a server to request a
migration to a new preferred address.

--- middle

# Introduction

The QUIC transport protocol allows a client to migrate connections at any time
to any new address ({{Section 9 of QUIC-TRANSPORT}}). This allows the connection
to survive changes to the client's address. QUIC also allows a server to migrate
to a different address, but only a single time, and only to an address specified
at the start of a connection via the Server's Preferred Address ({{Section 9.6
of QUIC-TRANSPORT}}). For some applications, including those where the server
and client are peers, limiting the server to only a single migration at the
beginning is too limiting. This document specifies an extension to QUIC to allow
a server to request a migration to a new preferred address.

This document defines a new transport parameter that indicates support of this
extension and specifies a new frame type to inform the client of the server's
new preferred address.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Motivation

In peer to peer networks, the role of server and client is arbitrary. An
endpoint may serve as a client in one connection and a server in another.
Limiting connection migration to clients limits the flexibility of endpoints in
this network. A peer in this network would like to migrate all of its
connections, not just the ones it happens to be a client in.

While it is not the primary goal, this extension may also assist in NAT
traversal by migrating to a dynamically chosen server address. A server could
have a client connect over a relay, and later migrate to a direct connection
after applying NAT traversal techniques. The specific NAT traversal techniques
are out of scope of this document.

# Negotiating Extension Use

new_preferred_address (0xff0969d85c):

Clients advertise their support of this extension by sending the
new_preferred_address (0xff0969d85c) transport parameter ({{Section 7.4 of
QUIC-TRANSPORT}}) with an empty value. Sending this transport parameter signals
to the server that the client understands the NEW_PREFERRED_ADDRESS frame.

Servers MUST NOT send this transport parameter. A client that supports this
extension and receives this transport parameter MUST abort the connection with a
TRANSPORT_PARAMETER_ERROR.

Endpoints MUST NOT remember the value of this extension for 0-RTT.

# New Preferred Address Frame

A server can use an NEW_PREFERRED_ADDRESS frame to request the client to
migrate the connection to the provided server address. Upon receiving an
NEW_PREFERRED_ADDRESS, the client MAY initiate migration. If the
client does migrate it MUST adhere to the client behavior defined in {{Section
9.6 of QUIC-TRANSPORT}}.

The NEW_PREFERRED_ADDRESS is defined as follows:

~~~
NEW_PREFERRED_ADDRESS Frame {
  Type (i) = 0x1d5845e2,
  Sequence Number (i),
  IPv4 Address (32),
  IPv4 Port (16),
  IPv6 Address (128),
  IPv6 Port (16),
}
~~~

Following the common frame format described in {{Section 12.4 of
QUIC-TRANSPORT}}, NEW_PREFERRED_ADDRESS frames have a type of 0x1d5845e2, and
contain the following fields:

Sequence Number:

: A variable-length integer representing the sequence number assigned to the
  NEW_PREFERRED_ADDRESS frame by the sender so receivers can ignore obsolete
  frames. A sending endpoint MUST send monotonically increasing values in the
  Sequence Number field to allow obsolete NEW_PREFERRED_ADDRESS frames to be
  ignored when packets are processed out of order.

IPv4 and IPv6 Address and Port:

: Analogous to the preferred_address transport parameter, this frame contains an
  address and port for both IPv4 and IPv6. The four-byte IPv4 Address field is
  followed by the associated two-byte IPv4 Port field. This is followed by a
  16-byte IPv6 Address field and two-byte IPv6 Port field.

NEW_PREFERRED_ADDRESS frames are ack-eliciting, and MUST only be sent in the
application data packet number space.

The server SHOULD ensure that its peer has a sufficient number of available and
unused connection IDs, as the client will be unable to migrate without an unused
connection ID. The server MAY bundle a NEW_CONNECTION_ID frame with the
NEW_PREFERRED_ADDRESS. Likewise, the client should ensure the same to allow the
server to probe new paths.


# Security Considerations

## Request Forgery Attacks

The same considerations from {{Section 21.5 of QUIC-TRANSPORT}} apply here as
well.

## DDoS - Thundering herd

A malicious server could wait until it has received a large number of clients,
and request a migration from all of them at the same time to a victim endpoint.
If the clients all migrate at the same time, they may overload or otherwise
negatively impact the victim endpoint.

Clients may mitigate this by randomly delaying the migration.

# IANA Considerations

## QUIC Transport Parameter

This document registers the new_preferred_address transport parameter in the "QUIC
Transport Parameters" registry established in {{Section 22.3 of
QUIC-TRANSPORT}}. The following fields are registered:

Value:
: 0xff0969d85c

Parameter Name:
: new_preferred_address

Status:
: Provisional

Specification:
: This document

Change Controller:
: IETF (iesg@ietf.org)

Contact:
: Marco Munizaga (marco@marcopolo.io)

## QUIC Frame Types

This document registers one new value in the "QUIC Frame Types" registry
established in {{Section 22.4 of QUIC-TRANSPORT}}. The following fields are
registered:

Value:
: 0x1d5845e2

Frame Type Name:
: NEW_PREFERRED_ADDRESS

Status:
: Provisional

Specification:
: This document

Change Controller:
: IETF (iesg@ietf.org)

Contact:
: Marco Munizaga (marco@marcopolo.io)

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# TODOs
{:numbered="false"}

# Questions
{:numbered="false"}

- Any new security conserations from allowing a dynamically chosen preferred
  address?

- Any new security conserations from allowing a deferred chosen preferred
  address?
