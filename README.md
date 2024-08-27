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

The following repositories contain various outcomes of this project.

### SASL client demonstration components

See https://github.com/peacekeeper/java-sasl-client-demo for a description.

### SASL server demonstration components

See https://github.com/peacekeeper/java-sasl-server-demo for a description.

### SASL local "Hello World" demonstration

See https://github.com/peacekeeper/java-sasl-local-demo for a description.

### Implementation of a DID-based SASL authentication mechanism

See https://github.com/peacekeeper/java-sasl-did-mechanism for a description.

### XMPP server using the DID-based SASL authentication mechanism

See https://github.com/peacekeeper/java-sasl-xmpp-server for a description.

### XMPP client using the DID-based SASL authentication mechanism

See https://github.com/peacekeeper/java-sasl-xmpp-client for a description.

## Additional Resources

The following additional resources have been used during research and preparation of this project, and are useful
for understanding SASL and its applications:

- SASL for HTTP - Explanation: http://internetwide.org/blog/2018/11/15/somethings-cooking-4.html
- SASL for HTTP - Specification: https://datatracker.ietf.org/doc/html/draft-vanrein-httpauth-sasl
- Java SASL - Documentation: https://docs.oracle.com/javase/8/docs/technotes/guides/security/sasl/sasl-refguide.html
- Java SASL - Tutorial: https://www.baeldung.com/java-sasl
- Existing SASL mechanisms: https://www.iana.org/assignments/sasl-mechanisms/sasl-mechanisms.xhtml
- Cyrus SASL plugin: https://gitlab.com/arpa2/kip/-/tree/master/contrib/cyrus-sasl2

## About

Markus Sabadello - https://github.com/peacekeeper/

<img align="left" height="40" src="https://github.com/peacekeeper/did-based-sasl/blob/main/docs/logo-ngi-assure.png?raw=true">

This project has received financial support from NLnet and the NGI Assure fund. NGI Assure was established with
financial support from the European Commission's Next Generation Internet programme, under the aegis of DG
Communications Networks, Content and Technology.
