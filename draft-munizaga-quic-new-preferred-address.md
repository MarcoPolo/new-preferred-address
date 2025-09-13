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
title: "QUIC New Server Preferred Address"
category: std

docname: draft-munizaga-quic-new-preferred-address-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: Transport
workgroup: QUIC
keyword:
 - Connection Migration
 - Server's Preferred Address

author:
 -
    fullname: Marco Munizaga
    organization: Ethereum Foundation
    email: marco@marcopolo.io

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

...

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

TODO

# Negotiating Extension Use

new_preferred_address (0xff0969d85c):

Clients advertise their support of this extension by sending the
new_preffered_address (0xff0969d85c) transport parameter {{Section 7.4 of
QUIC-TRANSPORT}}.

Servers do not need to send this transport parameter. Receiving this transport
parameter signals to the server that the client understands the New Preferred
Address frame.

# New Preferred Address Frame

A server can use an NEW_PREFERRED_ADDRESS frame to request the client to
migrate the connection to the provided server address. Upon receiving an
NEW_PREFERRED_ADDRESS, the client SHOULD initiate migration. If the
client does migrate it MUST adhere to the client behavior defined in {{Section
9.6 of QUIC-TRANSPORT}}, with the modification of allowing a migration to a new
server address mid-connection.

The NEW_PREFERRED_ADDRESS is defined as follows:

~~~
NEW_PREFERRED_ADDRESS Frame {
  IPv4 Address (32),
  IPv4 Port (16),
  IPv6 Address (128),
  IPv6 Port (16),
}
~~~


# Security Considerations

TODO Security

# IANA Considerations

This document defines a new

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# TODOs
{:numbered="false"}

- Rename draft to "New Server Preferred Address"
