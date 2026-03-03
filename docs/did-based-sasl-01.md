---
coding: utf-8

title: The DID-CHALLENGE SASL Mechanism
abbrev: did-challenge-sasl
docname: draft-did-challenge-sasl-01
category: info
date: 2026-03-03
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

This specification introduces a SASL mechanism based on Decentralized Identifiers (DIDs).
Unlike most other SASL mechanisms, this one is based on private/public key pairs and cryptographic signatures, rather than
digests or plain password exchange. DIDs are designed to be decentralized, persistent, cryptographically verifiable, and
resolvable identifiers.

For example, this can make it possible to
log in to your email account, IRC server, XMPP server, etc. using a DID, which can improve both usability and security.

--- middle

# Introduction

Many Internet protocols require authentication, e.g. when we check our email account with a username
and password, when we authenticate to SSH hosts with public keys, or when we log in to websites
using OpenID Connect.

[Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/) are identifiers that have associated private keys
and can be used for authentication purposes. DIDs can function as a replacement for usernames/passwords or static public keys,
since you can "authenticate" by proving control of your DID. Unlike other identifiers such as usernames
or domain names, DIDs do not require a central authority for creating and using them.

The [Simple Authentication and Security Layer (SASL)](https://www.rfc-editor.org/rfc/rfc4422.html) is an extensible
framework for authentication in Internet protocols. It makes it possible to "plug in" authentication mechanisms into
existing protocols, by decoupling the authentication mechanisms from the application protocols.

This specification introduces a DID-based SASL mechanism. For example, this can make it possible to
log in to your email account, IRC server, XMPP server, etc. using a DID, which can improve both usability and security.
In this specification, the SASL client has the role of a DID controller, and the SASL server has the role of a DID Resolver.

This specification also introduces optional support for [Verifiable Credentials (VCs)](https://www.w3.org/TR/vc-data-model-2.0/).
VCs express claims about a subject, such as name, date of birth, citizenship, employment by a company, membership in a
club, or any other semantic statement. VCs are typically exchanged between Issuers, Holders, and Verifiers. In this
specification, the SASL client has the role of a VC Holder, and the SASL server has the role of a VC Verifier.

Unlike many other SASL mechanisms, this one is based on private/public key pairs and cryptographic signatures, rather than
digests or plain password exchange.

# SASL mechanism name

The name of the DID-based SASL mechanism is "DID-CHALLENGE".

# Authentication

This section describes the interaction between a SASL client and SASL server that use
the "DID-CHALLENGE" mechanism.

## The Authentication Exchange

The "DID-CHALLENGE" mechanism is a server-first mechanism.

The exchange consists of the following steps:

~~~
C: Request authentication exchange
S: DID challenge
C: DID response
S: Outcome of authentication exchange
~~~

The mechanism is capable of transferring authorization identity strings (see [](#authorization-identity-string)).

The server is not expected to provide additional data when indicating a successful outcome.

As security layers, the mechanism supports data integrity and data confidentiality, using DID-based signatures,
and the TLS protocol.

During the exchange, the authorization identity is integrity-protected by a cryptographic signature.

## Authorization Identity String

In the "DID-CHALLENGE" mechanism, the [authorization identity string](https://www.rfc-editor.org/rfc/rfc4422#section-3.4.1)
is a DID as defined by [W3C DID Core - DID Syntax](https://www.w3.org/TR/did-1.0/#did-syntax), and percent-encoded as defined by
[RFC3986 Section 2.1](https://www.rfc-editor.org/rfc/rfc3986#section-2.1).

Example authorization identity string:

~~~
did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D 4RC7Rj4FCUe53AWyLEjYAgpRdpatwXaEN4kT4npALyuswait4m3Ai5KPpWABsVuqZyTfFGkGKWyeeb9QvXWgEQhh
~~~

## DID Challenge

The DID challenge follows the following format:

~~~
"<" <nonce> "." <timestamp> "@" <realm> ">"
~~~

Where:

- `<nonce>` MUST be a unique string.
- `<timestamp>` MUST be a UNIX timestamp.
- `<realm>` MUST be a SASL realm.

Example:

~~~
<7795631894096664932.1765144656954@java-sasl-xmpp-server>
~~~

## DID Response

The DID response follows the following format:

~~~
<did> <signature>
~~~

Where:

- `<did>` MUST be a Decentralized Identifier (DID) as defined in [W3C DID Core - DID Syntax](https://www.w3.org/TR/did-1.0/#did-syntax).
- `<signature>` MUST be a base64-encoded signature of the challenge 

Example:

~~~
did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D 4RC7Rj4FCUe53AWyLEjYAgpRdpatwXaEN4kT4npALyuswait4m3Ai5KPpWABsVuqZyTfFGkGKWyeeb9QvXWgEQhh
~~~

## Verification

The signature in the initial response MUST cover the entire initial challenge, and is generated using the DID's associated private key.

The server MUST perform the following verification steps:

- Resolve the DID to its DID document, according to the [W3C DID Resolution specification](https://www.w3.org/TR/did-resolution/).
- Retrieve the public keys from the DID document which have an "authentication" verification relationship, according to [W3C DID Core - Authentication](https://www.w3.org/TR/did-1.0/#authentication).
- Using the public keys from the DID document, verify the signature in the initial response against the initial challenge.
- Verify that the challenge's nonce has not been re-used.
- Verify that the challenge's timestamp is not too long in the past, e.g. 5 minutes.

# SASL Exchange with DIDs

This section illustrates the detailed steps of the SASL exchange.

The flow includes the DID challenge (see [](#did-challenge)) and DID response (see [](#did-response)) steps.

~~~ plantuml-utxt
title "The DID-CHALLENGE SASL mechanism"
participant ProtocolClient as "Protocol Client"
participant SASLClient as "SASL Client"
participant SASLServer as "SASL Server"
participant ProtocolServer as "Protocol Server"
participant DIDResolver as "DID Resolver"
ProtocolClient-->ProtocolServer: Network Connection
ProtocolClient->>SASLClient: Start login
SASLClient->>ProtocolClient: NameCallback for DID
ProtocolClient->>SASLClient: DID
note left of SASLClient: did%3Akey%3A<..did..>
SASLClient->>ProtocolClient: JWKCallback for DID private key
ProtocolClient->>SASLClient: DID private key
note left of SASLClient: { "kty": "OKP", "crv": "Ed25519", "x": "..", "d": ".." }
SASLClient->>SASLServer: Start SASL authentication
SASLServer->>SASLClient: List of authn mechanisms
SASLClient->>SASLServer: Selected authn mechanism "DID-CHALLENGE"
SASLServer->>SASLServer: Generate challenge
note left of SASLServer: <1809528678543235072.1724868615672@hostname>
SASLServer->>SASLClient: Challenge (nonce, timestamp, hostname)
SASLClient->>SASLClient: Create signature
note right of SASLClient: <..signature..>
SASLClient->>SASLServer: Response (DID, signature)
note left of SASLServer: did%3Akey%3A<..did..> 2mJ4tBo6H<..signature..>
SASLServer->>DIDResolver: Resolve DID
DIDResolver->>SASLServer: DID document with DID public key
SASLServer->>SASLServer: Verify signature
note right of SASLServer: true
SASLServer->>ProtocolServer: NameCallback with DID
ProtocolServer->>SASLServer: (empty)
SASLServer->>ProtocolServer: AuthorizeCallback
ProtocolServer->>SASLServer: authorized=true with DID
SASLServer->>SASLClient: Completed SASL authentication
~~~

## Step 1: Client NameCallback for DID

When the client is initialized, it obtains a DID to be used for authentication.

    -- CLIENT CALLBACK: NameCallback
        
    >C Client DID:  --- defaultName: null, name: null
    getName() -> did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D
    C> DID:  --- defaultName: null, name: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D

## Step 2: Client JWKCallback for Private Key

When the client is initialized, it obtains a private key that will be used for
signing challenges.

    -- CLIENT CALLBACK: JWKCallback
    
    >C Client private key:  --- defaultText: (JWK), text: null
    getTextInputJWK() -> {
        "kid": "did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D#z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D",
        "kty": "OKP",
        "crv": "Ed25519",
        "x": "EbV6-hVmDiD3DKTUgsf2SjjnO7t0ttwMhStQ5JyCFhw",
        "d": "vGjHIZzZxS3R4mo-V0I_S72ULXDqa2INqkAtuvqJUN8"
    }
    C> Private key:  --- defaultText: (JWK), text: {
      "kid": "did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D#z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D",
      "kty": "OKP",
      "crv": "Ed25519",
      "x": "EbV6-hVmDiD3DKTUgsf2SjjnO7t0ttwMhStQ5JyCFhw",
      "d": "vGjHIZzZxS3R4mo-V0I_S72ULXDqa2INqkAtuvqJUN8"
    }

## Step 3: Server -> Client Challenge

The server initiates the authentication flow by generating and sending a challenge. The challenge
contains a none, timestamp, and realm.

    -- SERVER -> CLIENT: Challenge
    <4513455346757278126.1757192932938@java-sasl-xmpp-server>

## Step 4: Client Signature

The client signs the challenge using the DID's private key.

    -- CLIENT
    Created signature for challenge <4513455346757278126.1757192932938@java-sasl-xmpp-server>: 4oxnhDjB6cZNKYbLbPcmpaKgimdN88bK45EvMizM6t1XEJ8MBYnymMiCpiu3qVEjQG2atVrbaARcKpHRiMrvDAeK

## Step 5: Client -> Server Response

The client response to the server with the DID and the signed challenge.

    -- CLIENT -> SERVER: Response
    did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D 4oxnhDjB6cZNKYbLbPcmpaKgimdN88bK45EvMizM6t1XEJ8MBYnymMiCpiu3qVEjQG2atVrbaARcKpHRiMrvDAeK

## Step 6: Server NameCallback with DID

The server obtains the DID from the client's response.

    -- SERVER CALLBACK: NameCallback
    
    >S DID:  --- defaultName: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, name: null
    checkName(did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D) --> did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D
    S> DID:  --- defaultName: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, name: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D

## Step 7: Server Verification

The server verifies the signature in the client's response by resolving the client's DID to a DID document, which
contains public keys need for the verification.

    -- SERVER
    Verified signature 4oxnhDjB6cZNKYbLbPcmpaKgimdN88bK45EvMizM6t1XEJ8MBYnymMiCpiu3qVEjQG2atVrbaARcKpHRiMrvDAeK for challenge <4513455346757278126.1757192932938@java-sasl-xmpp-server>: true

## Step 8: Server AuthorizeCallback with authorization ID

The server determines the DID as the "authorized ID", concluding the authentication flow.

    -- SERVER CALLBACK: AuthorizeCallback
    
    >S --- authenticationID: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizationID: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizedID: null, isAuthorized: false
    S> --- authenticationID: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizationID: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizedID: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, isAuthorized: true
    
    authorizationId: did%3Akey%3Az6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D

# (Optional) Authentication with VCs/VPs

This section defines an optional extension of the "DID-CHALLENGE" SASL mechanism which adds support for Verifiable Credentials (VCs)
and Verifiable Presentations (VPs).

## The Authentication Exchange (with VC/VP support)

The exchange consists of the following steps (expanding on [](#authentication)):

~~~
C: Request authentication exchange
S: DID challenge
C: DID response
S: VC/VP challenge
C: VC/VP response
S: Outcome of authentication exchange
~~~

The steps VC/VP challenge and response steps may be repeated multiple times.

## VC-VP Challenge

The VC/VP challenge follows the following format:

~~~
"<" <nonce> "." <timestamp> "." <vc.type> "@" <realm> ">"
~~~

Where:

- `<nonce>` MUST be a unique string.
- `<timestamp>` MUST be a UNIX timestamp.
- `<vc.type>` MUST be a type of a Verifiable Credential as defined in [W3C Verifiable Credentials Data Model v2.0 - Types](https://www.w3.org/TR/2025/REC-vc-data-model-2.0-20250515/#types).
- `<realm>` MUST be a SASL realm.

Example:

~~~
<7795631894096664932.1765144656954.DegreeCredential@java-sasl-xmpp-server>
~~~

## VC-VP Response

The VC/VP response follows the following format:

~~~
<vp>
~~~

Where:

- `<vp>` MUST be a Verifiable Presentation as defined in [W3C Verifiable Credentials Data Model v2.0 - Verifiable Presentations](https://www.w3.org/TR/2025/REC-vc-data-model-2.0-20250515/#verifiable-presentations).

Example:

~~~
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://www.w3.org/ns/credentials/examples/v2"
  ],
  "id": "urn:uuid:3978344f-8596-4c3a-a978-8fcaba3903c5",
  "type": ["VerifiablePresentation"],
  "verifiableCredential": [{
    "id": "did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D"
    "type": ["DegreeCredential"]
  }]
}
~~~

## Verification

TODO The signature in the initial response MUST cover the entire initial challenge, and is generated using the DID's associated private key.

TODO The server MUST perform the following verification steps:

- TODO
- Verify "holder"

# (Optional) SASL Exchange with DIDs and VCs/VPs

This section illustrates the detailed steps of the SASL exchange with DIDs and VCs/VPs, building on [](#sasl-exchange-with-dids).

The flow includes the DID challenge (see [](#did-challenge)), DID response (see [](#did-response)),
VC/VP challenge (see [](#vc-vp-challenge)), and VC/VP response (see [](#vc-vp-response)). 

~~~ plantuml-utxt
title "The DID-CHALLENGE SASL mechanism with VCs"
participant ProtocolClient as "Protocol Client"
participant SASLClient as "SASL Client"
participant SASLServer as "SASL Server"
participant ProtocolServer as "Protocol Server"
participant DIDResolver as "DID Resolver"
ProtocolClient-->ProtocolServer: Network Connection
ProtocolClient->>SASLClient: Start login
SASLClient->>ProtocolClient: NameCallback for DID
ProtocolClient->>SASLClient: DID
note left of SASLClient: did%3Akey%3A<..did..>
SASLClient->>ProtocolClient: JWKCallback for DID private key
ProtocolClient->>SASLClient: DID private key
note left of SASLClient: { "kty": "OKP", "crv": "Ed25519", "x": "..", "d": ".." }
SASLClient->>SASLServer: Start SASL authentication
SASLServer->>SASLClient: List of authn mechanisms
SASLClient->>SASLServer: Selected authn mechanism "DID-CHALLENGE"
SASLServer->>SASLServer: Generate challenge
note left of SASLServer: <1809528678543235072.1724868615672@hostname>
SASLServer->>SASLClient: Challenge (nonce, timestamp, hostname)
SASLClient->>SASLClient: Create signature
note right of SASLClient: <..signature..>
SASLClient->>SASLServer: Response (DID, signature)
note left of SASLServer: did%3Akey%3A<..did..> 2mJ4tBo6H<..signature..>
SASLServer->>DIDResolver: Resolve DID
DIDResolver->>SASLServer: DID document with DID public key
SASLServer->>SASLServer: Verify signature
note right of SASLServer: true
SASLServer->>ProtocolServer: NameCallback with DID
ProtocolServer->>SASLServer: (empty)
SASLServer->>ProtocolServer: AuthorizeCallback
ProtocolServer->>SASLServer: authorized=true with DID
SASLServer->>SASLClient: Completed SASL authentication
~~~

# Implementations

The following repositories contain various parts of an example implementation:

* SASL client demonstration components: [https://github.com/peacekeeper/java-sasl-client-demo](https://github.com/peacekeeper/java-sasl-client-demo)
* SASL server demonstration components: [https://github.com/peacekeeper/java-sasl-server-demo](https://github.com/peacekeeper/java-sasl-server-demo)
* SASL local "Hello World" demonstration: [https://github.com/peacekeeper/java-sasl-local-demo](https://github.com/peacekeeper/java-sasl-local-demo)
* Implementation of a DID-based SASL authentication mechanism: [https://github.com/peacekeeper/java-sasl-did-mechanism](https://github.com/peacekeeper/java-sasl-did-mechanism)
* XMPP server (based on Tigase) using the DID-based SASL authentication mechanism: [https://github.com/peacekeeper/java-sasl-xmpp-server](https://github.com/peacekeeper/java-sasl-xmpp-server)
* XMPP client demo (based on Tigase) using the DID-based SASL authentication mechanism: [https://github.com/peacekeeper/java-sasl-xmpp-client-tigase](https://github.com/peacekeeper/java-sasl-xmpp-client-tigase)
* XMPP client demo (based on Smack) using the DID-based SASL authentication mechanism: [https://github.com/peacekeeper/java-sasl-xmpp-client-smack](https://github.com/peacekeeper/java-sasl-xmpp-client-smack)
* XMPP client plugin (based on Spark) using the DID-based SASL authentication mechanism: [https://github.com/peacekeeper/java-sasl-xmpp-client-spark](https://github.com/peacekeeper/java-sasl-xmpp-client-spark)
* XMPP client application (based on Spark) using the DID-based SASL authentication mechanism: [https://github.com/peacekeeper/java-sasl-xmpp-client-spark](https://github.com/peacekeeper/java-sasl-xmpp-client-spark)
