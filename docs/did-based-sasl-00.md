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

This specification introduces a DID-based SASL mechanism. For example, this can make it possible to
log in to your email account, IRC server, XMPP server, etc. using a DID, which can improve both usability and security.

Unlike most other SASL mechanisms, this one is based on private/public key pairs and cryptographic signatures, rather than
digests or plain password exchange.

# SASL mechanism name

The name of the DID-based SASL mechanism is "DID-CHALLENGE".

# Authentication

This section describes the interaction between a SASL client and SASL server that use
the "DID-CHALLENGE" mechanism.

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
note left of SASLClient: did:key:<..did..>
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
note left of SASLServer: did:key:<..did..> 2mJ4tBo6H<..signature..>
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
    getName() -> did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D
    C> DID:  --- defaultName: null, name: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D

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
    <4513455346757278126.1757192932938@localhost>

## Step 4: Client Signature

The client signs the challenge using the DID's private key.

    -- CLIENT
    Created signature for challenge <4513455346757278126.1757192932938@localhost>: 4oxnhDjB6cZNKYbLbPcmpaKgimdN88bK45EvMizM6t1XEJ8MBYnymMiCpiu3qVEjQG2atVrbaARcKpHRiMrvDAeK

## Step 5: Client -> Server Response

The client response to the server with the DID and the signed challenge.

    -- CLIENT -> SERVER: Response
    did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D 4oxnhDjB6cZNKYbLbPcmpaKgimdN88bK45EvMizM6t1XEJ8MBYnymMiCpiu3qVEjQG2atVrbaARcKpHRiMrvDAeK

## Step 6: Server NameCallback with DID

The server obtains the DID from the client's response.

    -- SERVER CALLBACK: NameCallback
    
    >S DID:  --- defaultName: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, name: null
    checkName(did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D) --> did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D
    S> DID:  --- defaultName: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, name: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D

## Step 7: Server Verification

The server verifies the signature in the client's response by resolving the client's DID to a DID document, which
contains public keys need for the verification.

    -- SERVER
    Verified signature 4oxnhDjB6cZNKYbLbPcmpaKgimdN88bK45EvMizM6t1XEJ8MBYnymMiCpiu3qVEjQG2atVrbaARcKpHRiMrvDAeK for challenge <4513455346757278126.1757192932938@localhost>: true

## Step 8: Server AuthorizeCallback with authorization ID

The server determines the DID as the "authorized ID", concluding the authentication flow.

    -- SERVER CALLBACK: AuthorizeCallback
    
    >S --- authenticationID: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizationID: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizedID: null, isAuthorized: false
    S> --- authenticationID: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizationID: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, authorizedID: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D, isAuthorized: true
    
    authorizationId: did:key:z6MkfePUhxLV6cM54cgZ4bGmnEdTNm3WDf4arwh5kR3dH51D

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
