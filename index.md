# Depeche

This is the home of the Depeche protocol.

Depeche is a personal messaging protocol that enables communication even when the Internet is
not available - At the same time offering anonymity and confidentiality for both sender and
receiver. All at the small cost of reliability!

For an implementation of the protocol, [see DeDe, the Depeche demonstrator](https://github.com/depeche-protocol/dede)

## Use cases

For use when the internet is broken. Maybe an extended power-outage, maybe a natural disaster
such as a flood or earthquake, maybe a man made outage due to war, terror attack or government
shutdown, maybe just poverty or poor telephone coverage.

It is **not** for instant messaging, sending streaming data or surfing the web with low latency.
By its very nature, latency is high and message deliery is up to either deliberate delivery or
good fortune.

## How it works

The broad overview is that each participaring device (henceforth called a *node*) in the Depeche
network may generate messages. These messages are encrypted and addressed to another node and
then passed from node to node until it (hopefully) reaches its destination.

In order to bootstrap participation in the Depeche network, an operation called a *rendezvous*
is needed. During the rendezvous, your own addresses get known by at least one other node and
can thence be distributed to other node operators tha may wish to contact you.

The protocol consist of several parts, some of them form the "core" protocol, others parts
are meant to facilitate special cases or specialized usage.

### Message format

A message transmitted through use of the Depeche protocol (henceforth called a *depeche*, in
order to differentiate from other types of messages) is formatted as a JSON structure with
a few headers and an encrypted payload. The encrypted payload can be decrypted by a recipient
node and the contents should be a MIME-formatted message. The MIME payload is usually a
multipart message. Some message types form their own part of the Depeche protocol.

For a detailed specification, see ()

### Rendezvous

A fundamental part of the bootstrapping of a nodes participation in exchanging depeches is
the rendezvous. Rendezvous is envisioned as possible over several different carrier protocols,
but at present, the only well specified such methid is over a local network through UDP and
TCP/IP.

### Message exchange

Exchanging depeches among nodes is the fundamental way of transporting information through
the Depeche network.

## Current state

The current state of the protocol is that the basic functionality has been specified and
that this is covered by a reference implementation (included in this repository) has been
coded in Python. The current operations are:

* Rendezvous on an ethernet network
* Message exchange among nodes on a local network
* Forwarding of node addresses (own or foreign) via messages

## Areas of improvement

* Message priority schemes (mitigates flooding DOS attacks)
* Message signing (might be good or bad, depending on circumstances)
* Internet support (when the Internet is available, make use of it)