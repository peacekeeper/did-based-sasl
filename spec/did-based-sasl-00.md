---
coding: utf-8

title: The DID-CHALLENGE SASL Mechanism
abbrev: did-challenge-sasl
docname: draft-did-challenge-sasl-00
category: info
date: 2025-08-11
ipr: none

area: Security
wg: Common Authentication Technology Next Generation

stand_alone: yes
pi: [toc, sortrefs, symrefs, comments]

author:
  -
    ins: M. Sabadello
    name: Markus Sabadello
    org: Danube Tech GmbH
    street: Margaretenstraße 70/1/7
    city: Wien
    code: A-1050
    country: Austria
    phone: +43-664-3154848
    email: markus@danubetech.com

--- abstract

insert abstract here

--- middle

# Introduction

This MAY {{?RFC2119}} be useful.


Many Internet protocols require authentication, e.g. when we check our email account with a username
and password, when we authenticate to SSH hosts with public keys, or when we log in to websites
using OpenID Connect.

[Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/) are identifiers that have
associated private keys
and can be used for authentication purposes. DIDs are in practice mostly used for exchanging
[Verifiable Credentials (VCs)](https://www.w3.org/TR/vc-data-model-2.0/) between Issuers, Holders, and Verifiers.
On a more general level however, DIDs can also be used as a replacement for usernames/passwords or static public keys,
since you can "authenticate" by proving control of your DID. Unlike other identifiers such as usernames
or domain names, DIDs do not require a central authority for creating and using them.

The [Simple Authentication and Security Layer (SASL)](https://www.rfc-editor.org/rfc/rfc4422.html) is an extensible
framework for authentication in Internet protocols. It makes it possible to "plug in" authentication mechanisms into
existing protocols, by decoupling the authentication mechanisms from the application protocols.

This project is about integrating DIDs into existing Internet protocols that require
authentication, by leveraging the SASL framework. The idea is that for example you could log in to your SSH host,
email account, IRC server, XMPP server, etc. using your DID, which can improve both usability and security.
