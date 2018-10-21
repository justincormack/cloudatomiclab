+++
author = "Justin Cormack"
date = "2018-09-22T12:00:00Z"
description = "Distributed Capabilities via Cryptography"
tags = []
title = "Distributed Capabilities via Cryptography"
+++

<img src="/hammer-robot.jpg" width="80%"/>

This is a follow up to [my previous capabilities 
post](https://www.cloudatomiclab.com/noise-capabilities/). As before, you 
probably want to read [Capability Myths 
Demolished](http://srl.cs.jhu.edu/pubs/SRL2003-02.pdf) and the [Noise Protocol 
specification](https://noiseprotocol.org/noise.html) first for full value 
extraction. This is a pretty rough draft, I was going to rewrite it but decided
just to publish as is, work in progress, and write another post later, having
left it for several weeks after writing it. This stuff needs a much clearer
explanation.

I went to a [Protocol Labs](https://protocol.ai) dinner last night (thanks for 
the invite Mike) and managed to corner Brian Warner from 
[Agoric](https://agoric.com/) and ask about cryptographic distributed 
capabilities, which was quite helpful. This stuff has not really been written 
down, so here is an attempt to do so. I should probably add references at some 
point.

## Non cryptographic capabilities

For reference and completeness, let us cover how you transmit capabilities 
without any cryptography, and what the downsides are. The basic model is called 
the "Swiss number", after a (I suspect somewhat mythical) model of an anonymous 
Swiss bank account, where you just present the account number, which you must 
keep secret, in order to deposit and withdraw money, no questions asked. This 
is pretty much the standard model in the historic literature, largely written 
before public key cryptography was feasible to use. In modern terms, the Swiss 
number capability should be a random 256 bit number, and the connection should 
of course be encrypted to prevent snooping. The implementation is easy, just 
check (in constant time!) that the presented number is equal to the object's 
number. Minting these is trivial. The capability is a random bearer token.

The downsides are pretty clear. First is that you may present the capability to 
the wrong party for checking. Checking and transfer of capabilities are very 
different operations, and we would like that checking did not reveal the token. 
This is a general problem with bearer tokens, such as JWT, they can easily be 
presented to the wrong party, or to a man in the middle attacker. We would like 
cryptographic protection for the check operation to avoid this. The second 
downside, which is somewhat related, is that we have no idea how to identify 
the intended object. Any party who has a copy of the capability can pretend to 
be the object it refers to, as there is no asymmetry between parties. We have 
to rely on some external naming system, that might be subverted. The third 
issue is that we have to build our own encryption, and the token we have does 
not help, as it does not act as a key or help identify the other party. So we 
have to rely on anonymous key exchange, which is subjet to man in the middle 
attacks as we do not know an identity for the other participant, or again some 
sort of external source of truth, such as the PKI system.

These downsides are pretty critical for modern secure software, so we need to 
do better. We will refer to these three properties, check does not reveal, 
object identifier, and encryption included to analyze some alternatives.

There are some things I am not going to discuss in this post. I mentioned the 
model of secret public keys, which appears in some of the literature, in an 
earlier post, but will ignore it here as it has security issues. I am not going 
to cover macaroons either; they are another form of bearer token with 
differently interesting properties.

## Cryptographic Capabilities

The obvious way to solve the second problem, of being able to identify the 
object that the capability refers to securely, is to give the object an 
asymmetric key. We can then hand out the public key, which can uusefully be the 
object identifier and be used to locate it, while the object keeps its private 
key secure, and does not hand it out to any other object (it can be kept in a 
TPM type device as it is only needed for restricted operations).
We can now set up an encrypted channel with this object, and as we know the 
public key up front, we can be sure we have connected to the right object if we 
validate this correctly. In Noise Protocol terms, we can use an NK handshake 
pattern, where the connecting object is anonymous but it knows the public key 
it is connecting to. We can also use XK (or IK) if we want to pass the object 
identity of the connecting object, for example for audit purposes. Once we have 
connected, we can use the Swissnum model to demonstrate we have the capability, 
but without the risk of passing the capability to the wrong party.

However, we can improve this, by using the Swissnum as a symmetric key, and 
incorporating it as a secret known by both parties into the asymmetric 
handshake. In Noise Protocol terms this is the NKpsk0 handshake (or XKpsk0) 
that I mentioned in my previous post. The handshake will only succeed if both 
parties have the same key, as the key is securely mixed into the shared 
symmetric key that is generated from the Diffie-Hellman exchange of the public 
keys. This is even better than the Swissnum method above, as the handshake is 
shorter as you do not need the extra phase to pass and potentially acknowledge 
the Swissnum; it looks pretty similar as a symmetric key is generally just an 
arbitrary random sequence of 256 bits or so anyway.

This model does solve all our three issues, as a handshake to the wrong party 
does not reveal the capability, the object cannot be spoofed by another 
(without stealing the private key) and the keys support and encrypted channel. 
It is not the only mechanism however. Minting new capabilities is easy, you 
just create a new symmetric key, and creating objects is easy, create an 
asymmetric keypair.

Instead of using an asymmetric key and a symmetric key, Brian Warner pointed 
out to me yesterday that we can present a certificate instead of the symmetric 
key. This is slightly more complex. To demonstrate possession of a capability, 
we will present a certificate to the object. We have to sign an ephemeral that 
the object presents us, and the simplest method is if the object that the 
capability is for has the public key to check the signature, and the capability 
is the private signing key. Anyone with the capability can directly sign the 
certificate, and you pass the private key around to transfer the capability. 
Note that the subject of the capability does not need to know the private 
signing key, so it cannot necessarily pass on a capability to access itself. 
This might an advantage in some circumstances. Note that the holders of the 
capability need to transfer a private key to pass the capability on, so they 
cannot hold the key in a TPM device that does not allow key export, or indeed a 
general cryptographic API that only supports a private key type that has 
signing operations but not an export operation, which has been common practise. 
Note that the Noise Protocol Framework support for signatures is a work in 
progress, scheduled for a revision later this year.

If you don't want to pass around private keys, you could use a chained 
signature model, where each party that passes on a capability adds to a 
signature chain, authenticating the public key of the next party, all chaining 
down to the original key. This would mean unbounded lengths of chain though, 
that would be a problem for many use cases. It would provider an audit trail of 
how each party got the capability, but transparency logs probably do this more 
effectively.

Thinking about this model, actually we do not need to use signatures, we can 
just use encryption keys directly. The same as before the object the capability 
is granted over has a private encryption key, but instead of using signatures, 
we can create an asymmetric encryption keypair, and give the object the public 
key, while capability holders get the private key, and pass the private key 
around as the capability. So to validate an encryption handshake, the object 
will check that the capability holder has the correct private key, while the 
capability holder will validate it is talking to the object that possesses the 
identiy private key. In Noise protocol terms this is a KK handshake, where both 
parties know the public key for the other party, and verify that each possesses 
the private key. The signature version is a KK variant with one signature 
substituted for anencryption key, and there is another variant where both keys 
are replaced by signatures, the Noise signature protocol modifiers allow 
sigantures to substitute for longer term with ephemeral Diffie-Hellman key 
agreement in any combination, with some deferral modifictions.

So we see that rather than using the mixed symmetric and asymmetric key model 
(NKpsk) that I discussed before, we can use symmetric key only (KK) models for 
distributed capabilities. The differences for the user are relatively small, as 
both methods fulfil our three criteria, we just have the difference that the 
object need not be able to pass on capabilities to itself in the public key 
only model, and the fact that we have to pass around asymmetric private keys, 
which there is a reluctance to do sometimes. For quantum resistence, it is
possible to use a combination of both symmetric and asymmetric keys, sharing
a symmetric key among all parties.
