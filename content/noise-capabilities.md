+++
author = "Justin Cormack"
date = "2018-01-21T23:00:00Z"
description = "Using the Noise Protocol Framework to design a distributed capability system"
tags = []
title = "Using the Noise Protocol Framework to design a distributed capability system"
+++

<img src="/static.jpg" width="100%"/>

## Preamble

In order to understand this blog you should know about [capability-based 
security](https://en.wikipedia.org/wiki/Capability-based_security). Perhaps 
still the best introduction, especially if you are mainly familiar with role 
based access control is the [Capability Myths 
Demolished](http://srl.cs.jhu.edu/pubs/SRL2003-02.pdf) paper.

You will also need to be familiar with the [Noise Protocol 
Framework](http://noiseprotocol.org/noise.html). Noise is a fairly new crypto 
meta protocol, somewhat in the tradition of the NaCl Cryptobox: protocols you 
can use easily without error. It is used in modern secure applications like 
[Wireguard](https://www.wireguard.com/). Before reading the specification this 
short (20m) talk from Real World Crypto 2018 by Trevor Perrin, the author, is 
an excellent introduction.

<iframe width="560" height="315" 
src="https://www.youtube.com/embed/3gipxdJ22iM?rel=0" frameborder="0" 
allow="autoplay; encrypted-media" allowfullscreen></iframe>

## Simplicity

Our stacks have becoming increasingly complicated. One of the things I have 
been thinking about is protocols for lighter weight interactions. The smaller 
services get, and the more we want high performance services, the more the 
overhead of protocols designed for large scale monoliths don't perform. We 
cannot replace larger scale systems with nanoservices, serverless and Edge 
services if they cannot perform. In addition to performance, we need scaleable 
security and identity for nanoservices. Currently nanoservices and serverless 
are not really competitive in performance with larger monolithic code, which 
can serve millions of requests a second. Current serverless stacks hack around 
this by persisting containers for minutes at a time to answer a single request. 
Edge devices need simpler protocols too; you don't really want GRPC in 
microcontrollers. I will write more about this in future.

Noise is a great framework for simple secure crypto. In particular, we need 
understandable guarantees on the properties of the crypto between services. We 
also need a workable identity model, which is where capabilities come in.

## Capabilities

Capability systems, and especially not distributed capability systems, are not 
terribly widely used at present. Early designs included KeyKos, and the E 
language, which has been taken up as the [Cap'n Proto 
RPC](https://capnproto.org/rpc.html) design. Pony is also capability based, 
although these are somewhat different [deny 
capabilities](https://blog.acolyer.org/2016/02/17/deny-capabilities/). Many 
systems include some capability like pieces though; Unix file descriptors are 
capabilities for example, which is why file descriptor based Unix APIs are so 
useful for security.

With a large number of small services, we want to give out fine grained 
capabilities. With dynamic services, this is much the most flexible way of 
identifying and authorizing services. Capabilities are inherently 
decentralised, with no CAs or other centralised infrastructure; services can 
create and distribute capabilities independently, and decide on their trust 
boundaries. Of course you can also use them just for systems you control and 
trust too.

While it has been recognised for quite a while that there is [an equivalence 
between public key cryptography and 
capabilities(http://www.cap-lore.com/CapTheory/Dist/PubKey.html), this has not 
been used much. I think part of the reason is that historically, public key 
cryptography was slow, but of course computers are faster now, and encryption 
is much more important.

The correspondance works as follows. In order for Alice to send an encrypted 
message to Bob, she must have his public key. Usually, people just publish 
public keys so that anyone can send them messages, but if you do not 
necessarily do this things get more interesting. Possession of Bob's public key 
gives the capability of talking to Bob; without it you cannot construct an 
encrypted message that Bob can decode. Actually it is more useful to think of 
they keys in this case not as belonging to people but as roles or services. 
Having the service public key allows connecting to it; having the private key 
lets you serve it. Note you still need to find the service location to connect; 
a hash of the public key could be a suitable DNS name.

On single hosts, capabilities are usually managed by a privileged process, such 
as the operating system. This can give out secure references, such as small 
integers like file descriptors, or object pointers protected by the type 
system. These methods don't really work in a distributed setup, and 
capabilities need a representation on the wire. One of the concerns in the 
literature is that if a (distributed) capability is just a string of 
(unguessable) bits that can be distributed, then it might get distributed 
maliciously. There are two aspects to this. First if a malicious agent has a 
capability at all, it can use it maliciously, including proxying other 
malicious users, if it has network access. So being able to pass the capability 
on is no worse. Generally, only pass capabilities to trusted code, ideally code 
that is confined by (lack of) capabilities in where it can communicate and does 
not have access to other back channels. Don't run untrusted code. In terms of 
keys being exfiltrated unintentionally, this is also an issue that we generally 
have with private keys; with capabilities all keys become things that, 
especially in these times of Sceptre, we have to be very careful with. 
Mechanisms that avoid simply passing keys, and pass references instead, seem to 
me to be more complicated and likely to have their own security issues.

## Using Noise to build a capability framework

The Noise spec says "The XX pattern is the most generically useful, since it 
supports mutual authentication and transmission of static public keys." However 
we will see that there different options that make sense for our use case. The 
XX pattern allows two parties who do not know each other to communicate and 
exchange keys. The XX echange still requires some sort of authentication, such 
as certificates to see if the two parties should trust each other.

When using keys as capabilities though we always know the public key (aka 
capability) of the service we want to connect to. In [Noise 
spec](http://noiseprotocol.org/noise.html#interactive-patterns) notation, that 
is all the ones with `<- s` in the pre-message pattern. This indicates that 
prior to the start of the handshake phase, the responder (service) has sent 
their public key to the initiator (directly or indirectly). That is, that the 
initiator of the communication posseses the capability required to connect to 
the service, in capability speak. So these patterns are the ones that 
correspond to capability systems; for the interactive patterns that is `NK`, 
`KK` and `XK`.

`NK` corresponds to a communication where the initiator does not provide any 
identification. This is the normal situation for many capability systems; once 
you have the capability you perform an action. If the capability is public, or 
widely distributed, this corresponds more or less to a public web API, although 
with encryption.

`XK`, and `IK` are the equivalent in web terms of providing a (validated) login along with 
the connection. The initiator passes a public key (which could be a capability, 
or just used as a key) during the handshake. If you want to store some data 
which is attached to the identity you use as the passed public key, this 
handshake makes sense. Note that the initiator can create any number of public 
keys, so the key is not a unique identifier, just one chosen identity. `IK` is
the same semantics but has a different, shorter, handshake with slightly different
security properties; it is the one used by Wireguard.

`KK` is an unusual handshake in traditional capability terms; it requires that 
both parties know in advance each other's public key, ie that there is in a 
sense a mutual capability arrangement, or rendezvous. You could just connect 
with `XK` and then check the key, but having this in the handshake may make 
sense. An `XK` handshake could be a precursor to a `KK` relationship in future.

In addition to the more common two way handshakes, noise supports 
unidirectional one way messages. It is not common to use public key encryption 
for offline messages, such as encrypting a file or database record at present. 
Usually symmetric keys are used. The noise one way methods use public keys, and 
all three `N`, `X` and `K` require the recipient public key (otherwise they 
would not be confidential), so they all correspond to capabilities based 
exchanges. Just like the interactive patterns, they can be anonymous or pass or 
require keys. There are disadvantages to these patterns, as there is no replay 
protection, as the receiver cannot provide an ephemeral key, but for offline 
uses, such as store and forward, or file or database encryption. Unlike 
symmetric keys for this use case, there is a seperate sender and receiver role, 
so the ability to read database records does not mean the ability to forge 
them, improving security. It also fits much better in a capabilities world, and 
is simpler as there is only one type of key, rather than having two types and 
complex key management.

Noise also supports an additional pre shared symmetric key which both parties 
must know in addition to the asymetric keys. You could use this as an 
additional access gateway, to define a domain of communication, if you think 
the capabilities might leak. I don't thimkk this helps much as if the 
capabilities can leak the pre shared key can leak too. It could make sense for 
defining isolated systems that cannot communicate though; the main purpose is 
post quantum crypto resistence though. 

## Summary

I don't have an implementation right now. I was working on a prototype 
previously, but using Noise `XX` and still trying to work out what to do for 
authentication. I much prefer this design, which answers those questions. There 
are a bunch of practicalities that are needed to make this usable, and some 
conventions and guidelines around key usage.

We can see that we can use the Noise Protocol as a set of three interactive and 
three one way patterns for capability based exchanges. No additional 
certificates or central source of authorisation is needed other than public and 
private key pairs for Diffie Hellmann exchanges. Public keys can be used as 
capabilities; private keys give the ability to provide services. The system is 
decentralised, encrypted and simple. And there are interesting properties of
mutual capabilities that can be used if required.
