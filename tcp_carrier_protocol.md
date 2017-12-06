# Ethernet/WiFi rendezvous and message exchange protocol

## Introduction

This is only a first stab at using a TCP and UDP capable network for Depeche purposes.
Since it's pretty basic and simple (and possibly a bit stupid), it'll henceforth be called the
"naive" protocol.

In order to communicate directly node-to-node over an TCP capable network, some form of
transfer protocol built on sockets is needed. This describes the protocol used for
the majority of rendezvous and message exchange - In both of these cases, the entirety
of the transaction is a protocol built on top of an initial UDP broadcast phase, followed
by TCP connection.

### UDP broadcasts

Since nodes connecting to a network need some method of establishing whether there are
other nodes on the same network, the naive protocol uses an inital UDP broadcast.

### TCP as carrier protocol

The protocol is a lightweight frame transfer, half-duplex protocol in which the parts
politely take turns talking. Byte order is "network", also known as "big-endian".

#### A frame formatted like this:

* **4 bytes** - Protocol identifier 3725508833 [de0ec0e1] (Not to be
                treated as a delimiter, but will serve to reduce number
                of faulty reads)
* **2 bytes** - Version number of protocol. The current version is 0
* **4 bytes** - Size information - 32 bit unsigned integer indicating
                size of message to read. The value of this is called 'S'
* **1 byte**  - Boolean indicating of this is the last message in a
                sequence of messages. True, indicating end-of-sequence,
                if non-zero.
* **S bytes** - Message contents.

If the segment it not the last in the sequence, the receiver should expect a follow-up
package to continue the contents. The idea is to fit one or several depeches inside a
single frame of data, but if needed a very large depeche might be split across multiple
frames.

## Naive rendezvous protocol

TODO

## Naive message exchange protocol

TODO