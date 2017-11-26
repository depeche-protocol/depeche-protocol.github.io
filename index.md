# Depeche

This is the home of the Depeche protocol.

Depeche is a personal messaging protocol that enables communication even when the Internet is
not available - At the same time offering anonymity and confidentiality for both sender and
receiver. All at the small cost of reliability!

For an implementation of the protocol, [see DeDe](https://github.com/depeche-protocol/dede)

## Use cases

For use when the internet is broken. Maybe an extended power-outage, maybe a natural disaster
such as a flood or earthquake, maybe a man made outage due to war, terror attack or government
shutdown, maybe just poverty or poor telephone coverage.

It is **not** for instant messaging, sending streaming data or surfing the web with low latency.
By its very nature, latency is high and message delivery is up to either deliberate relay or
good fortune.

## How it works

The broad overview is that each participaring device (henceforth called a *node*) in the Depeche
network may generate messages. These messages are encrypted and addressed to another node and
then passed from node to node until it (hopefully) reaches its destination.

In order to bootstrap participation in the Depeche network, an operation called a *rendezvous*
is needed. During the rendezvous, your own addresses get known by at least one other node and
can thence be distributed to other node operators that may wish to contact you.

To conceal both sender and recipient, a system of one-time-addresses is used. Participants
exchange lists of these in the shape of *address pads* that are then used up over time. The
one-time nature of the addresses and the fact that only the sender and recipient knows them
make the protocol resistant to traffic analysis.

Messages are transferred promiscously - The reproduction cost of data is so close to zero that
this is affordable. This means that routing or message paths are not available for anaysis either.
The downside is that messages may easily get lost and never reach their intended recipient.

### An example of use

Alain wants to sent a depeche to a friend of his, Célestin, whom he rarely gets to meet. Alain and
Célestin have previously met and performed a rendezvous, so they have a store of each other addresses.
They have a common friend, Berthe, whom they both meet regularly and who is also a Depeche user.
Alain first composes his message and when he next meets Berthe, he asks her to exchange messages.
When Berthe next meets with Célestin and exchange messages, Alain's message will reach Célestin.

If Alain has no friend that he can rely on to relay his message, an option would be to just
keep exchanging messages with anyone available, in the hopes that the message will find its
way to Célestin. It might, but Alain can never be really sure until he receives a reply.

## Threat models

The protocol is intended primarily for use in **non-hostile environments**, such as during
emergencies or generally poor internet connectivity. In this kind of environment, the primary
threats are third parties interested in reading, modifying or interrupting individual
communications: A nosy neighbour, a business competitor, a creditor, a disgruntled relative etc.

For this kind of threat, the protocol offers anonymity of both sender and recipient. This means
that a third party cannot be sure of either origin or recipient, and hence cannot reliably
interrupt communications by other means than refusing to relay messages. Message encryption means
that the message payload cannot be eavesdropped or modified - A modified message would be
indecipherable even if the third party were to know the sender and recipient.

In **hostile, high threat environments** - such as under a repressive regime, the protocol can also
be used. The same protections as in the non-hostile situation applies, but in this case even
posession of encrypted messages might be a punishable offence. Extreme care needs to be excercised,
especially when it comes to rendezvous, which generally requires close physical proximity of
the participants. The protocol in itself is hence not enough to protect users in hostile
environments - A very high level of operational security needs to be maintained.

When usage of Depece faces adversaries, denial of service is a real threat - The easiest way to
affect that is through message flooding. This will force nodes to reject messages due to filling
of storage buffers. In order to combat this, some form of message priority is needed. There
are currently three different methods envisioned - They will be detailed at a later stage:

* Circle-of-friends (suitable for use by private persons)
* Trust hierarchy (suitable for use by organisations)
* Voice servers (suitable when trusted third party is available)

## Technical overview

The protocol consist of several parts, some of them form the "core" protocol, others parts
are meant to facilitate special cases or specialized usage.

### Message format

The message format itself forms the kernel of the protocol.
A message transmitted through use of the Depeche protocol (henceforth called a *depeche*, in
order to differentiate from other types of messages) is formatted as a JSON structure with
a few headers and an encrypted payload. The encrypted payload can be decrypted by a recipient
node and the contents should be a MIME-formatted message. The MIME payload is usually a
multipart message. Some message types form their own part of the Depeche protocol.

For a detailed specification, see [the message format specification](message_format.md)

### Rendezvous

A fundamental part of the bootstrapping of a nodes participation in exchanging depeches is
the rendezvous. Rendezvous is envisioned as possible over several different carrier protocols,
but at present, the only well specified such methid is over a local network through UDP and
TCP/IP.

### Message exchange

Exchanging depeches among nodes is the fundamental way of transporting information through
the Depeche network. Message exchange can occur in many ways - Currently the specifications
support mutual exchange over TCP by two typically non-server nodes, but other models are certainly
possible. Examples might include:

* Handover of USB sticks
* One-way radio transmissions
* General HTTP or FTP transfer

### Carrier protocols

Depeche is designed to be usable in many different circumstances, using different hardware
and communications capabilities. Carrier protocols are the underlying communications methods
that nodes may use to connect to each other. Though TCP and UDP are currently the only carrier
protocols that have specifications for how to do rendezvous and exchange, other possibilities
might include:

* Bluetooth
* NFC
* Audio modem
* Camera-and-screen (typically using mobile phones with high-res cameras and screens using QR codes)
* IrDA
* VHF radio

## Current state

The current state of the protocol is that the basic functionality has been specified and
that this is covered by a reference implementation, [DeDe](https://github.com/depeche-protocol/dede)
has been coded in Python. The current operations are:

* Rendezvous on a local TCP network, such as WiFi or ethernet
* Message exchange among nodes on a local network
* Forwarding of node addresses (own or foreign) via messages

## Areas of improvement

The protocol is still very much a work in progress and there are many ways in which it might
be improved and extended. This is just a taste of what remains:

* Message priority schemes (mitigates flooding DOS attacks)
* Message signing (might be good or bad, depending on circumstances)
* Internet support (when the Internet is available, make use of common carrier protocols such as HTTP)
* Signing of messages and letters of recommendation (increasing trust in the senders and reducing
  risk of MITM attacks)
* Persistent addresses (for use in initiating communication without rendezvous, though this has other
  risks and trust issues)