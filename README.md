# Securing Internet protocols with DIDs, using SASL

## Information

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

## Resources

### SASL documentation overview

### "Hello World" SASL demonstration

### Requirements document and threat model

### Specification(s) of DID-based SASL mechanism(s)

### Implementation of server-side and client-side components

### Open-source code and documentation

## About

Markus Sabadello - https://github.com/peacekeeper/

<img align="left" height="40" src="https://github.com/peacekeeper/did-based-sasl/blob/main/docs/logo-ngi-assure.png?raw=true">

This project has received financial support from NLnet and the NGI Assure fund. NGI Assure was established with
financial support from the European Commission's Next Generation Internet programme, under the aegis of DG
Communications Networks, Content and Technology.
