# Securing Internet protocols with DIDs, using SASL

## End-to-end demonstration

This page shows an end-to-end demonstration of the DID-based SASL mechanism, integrated with
an XMPP server and multiple XMPP clients.

This demonstration uses the components listed [here](./), which contain further documentation.

This demonstration is based on the work in this specification: https://peacekeeper.github.io/did-based-sasl/draft-sabadello-did-challenge-sasl-00.html

### Step 1

Running a local instance of the [`java-sasl-xmpp-server` component](https://github.com/peacekeeper/java-sasl-xmpp-server),
which is a configuration of the [Tigase XMPP Server](https://tigase.net/xmpp-server/), with added support for the
DID-based SASL authentication mechanism described and implemented in https://github.com/peacekeeper/java-sasl-did-mechanism.

![alt text](https://github.com/peacekeeper/did-based-sasl/blob/main/demo/demo1-screenshot1.png?raw=true)

### Step 2

Running a local instance of the [`java-sasl-xmpp-client-smack` component](https://github.com/peacekeeper/java-sasl-xmpp-client-smack),
which is a configuration of the [Smack library](https://github.com/igniterealtime/Smack), with added support for the
DID-based SASL authentication mechanism described and implemented in https://github.com/peacekeeper/java-sasl-did-mechanism.

This will authenticate via DID, and then send messages to the XMPP Server created in Step 1.

![alt text](https://github.com/peacekeeper/did-based-sasl/blob/main/demo/demo1-screenshot2.png?raw=true)

![alt text](https://github.com/peacekeeper/did-based-sasl/blob/main/demo/demo1-screenshot3.png?raw=true)

### Step 3

Running a local instance of the [`Spark` component](https://github.com/peacekeeper/Spark),
which is a configuration of the [Spark application](https://github.com/igniterealtime/Spark), with added support for the
DID-based SASL authentication mechanism described and implemented in https://github.com/peacekeeper/java-sasl-did-mechanism.

This support authentication via multiple mechanisms, including DIDs, and then be used to send messages to the
XMPP Server created in Step 1, and receive messages.

![alt text](https://github.com/peacekeeper/did-based-sasl/blob/main/demo/demo1-screenshot4.png?raw=true)

![alt text](https://github.com/peacekeeper/did-based-sasl/blob/main/demo/demo1-screenshot5.png?raw=true)

![alt text](https://github.com/peacekeeper/did-based-sasl/blob/main/demo/demo1-screenshot6.png?raw=true)

## About

Markus Sabadello - https://github.com/peacekeeper/

<img align="left" height="40" src="https://github.com/peacekeeper/did-based-sasl/blob/main/docs/logo-ngi-assure.png?raw=true">

This project has received financial support from NLnet and the NGI Assure fund. NGI Assure was established with
financial support from the European Commission's Next Generation Internet programme, under the aegis of DG
Communications Networks, Content and Technology.

<img align="left" height="40" src="https://github.com/peacekeeper/did-based-sasl/blob/main/docs/logo-ngi-zero.png?raw=true">

This project has received financial support from NLnet and the NGI0 Commons fund. NGI0 Commons was established with
financial support from the European Commission's Next Generation Internet programme, under the aegis of DG
Communications Networks, Content and Technology.
