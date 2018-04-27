# Message format

## Envelope

A well-formed message relayed through Depeche nodes is (perhaps unsurprisingly)
called a *depeche*. First a sample:

```
{
    "to_address": "23423423423444353453455345345345",
    "send_time": "2001-12-31T21:33:44",
    "forward_to": "http://deadlett.er/drop/1234"
    "contents": "aasdadfsdjsdgkjsfkdlajfksfjfghjkfhsjkdfhksdfakjdhfskjdfhdskjfh"
}
```

Obviously, the above depeche has contents that are not really decipherable, but
it is a legal format. The [JSON schema](http://json-schema.org) for a depeche is:

```
{
    "$schema": "http://json-schema.org/draft-07/schema#",
    "title": "depeche",
    "description": "A message as relayed in the Depeche protocol",
    "type": "object",
    "properties": {
        "to_address": {
            "description": "The address to which this depeche should be relayed; the recipient",
            "type": "string"
        }
        "send_time": {
            "description": "The time at which the original sender constructed the depeche"
            "type": "string"
        }
        "forward_to": {
            "description": "A location where the recipient might look for the message: a dead letter drop identifier"
            "type": "string"
        }
        "contents": {
            "description": "Base64 encoded encrypted message contents that, once decrypted, forms the payload of the depeche."
            "type": "string"
        }
    },
    "required": ["to_address", "contents"]
}
```

## Payload

The payload of the depeche is a MIME-message, usually a multipart mail message. A payload may
contain parts that convey information in for use in the protocol itself. These are currently:

* **Address pad request** - A message part that has `Content-Type: 'application/json'` and
  `Content-Description: 'depeche/address_pad_request'`
* **Address pad** - A message part that has `Content-Type: 'application/json'` and
  `Content-Description: 'address_pad'`

### Address pad request

A payload may contain an address pad request signalling a request by a foreign node to send an
address pad in reply. This is normal when the foreign node is running low on addresses to use.
In the case of a third-party introduction, an address pad request is almost certain to be attached
to the initial message by the introductee.

Example of an address pad request:
```
{
    "type": "address_pad_request",
    "requested_size": 42
}
```

The intended recipient is always to be assumed to be the originator of the request. Normally this
is discerned from the "From" field in the payload, but it can also be inferred from the address
used, the message text or any signature present.

### Address pad

An address pad is attached to the payload when the sending party wishes to send a batch of
addresses to the contact the depeche is sent to. In general, the recipient will know the sender but
in case of a third-party introduction the recipient may be given an address pad from a foreign node
that is unknown.

Example of an address pad:
```
{
    "type": "address_pad",
    "owner": "bigfish_4711"
    "mappings": [
        {
            "key": "47343783746ab374642233"
            "addresses": ["423432422342342234234234223", "353453453453466778674645"]
        },
        {
            "key": "473437837ccab234242343"
            "addresses": ["898984598475898799354587385", "347746646466234297453454"]
        }
    ]
}
```

The owner of the address pad is not always assumed to be the sender of the depeche - It is rather
common for nodes to forward addresses of foreign nodes to other foreign nodes. Since the alias a node
uses is by no means guaranteed to be consistent across its contacts, this could be cause for
confusion.

### Future possible parts

A few other possible parts might be useful. For example:

* Recommendaton - A letter of recommendaton for use in case of third-party introduction.
* Signature - A cryptographic proof-of-origin. Needed for recommendations to work as well as for
  (possibly dangerous) non-repudiation.