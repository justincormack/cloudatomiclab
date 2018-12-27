+++
author = "Justin Cormack"
date = "2018-12-26T20:00:00Z"
description = "QUIC for Unikernels"
tags = []
title = "QUIC for Unikernels"
+++

I had until recently mostly been ignoring QUIC. In case you had, QUIC is a 
new-ish protocol developed to Google, that will probably be HTTP version 3. The 
interesting pieces are that it runs over UDP not TCP, but supports reliable 
delivery by implementing retransmission itself, that it supports multiple 
streams without head of line blocking, and that it is designed to support 
encryption natively. Another important benefit is that connections can migrate 
from one IP to another without being dropped, as there is a connection ID 
independent of source and destination addresses. It is also designed to not 
ossify, with as much as possible of the packet encrypted as possible, so that 
intermediaries cannot inspect what is inside the packet and make decisions, to 
avoid the great difficulty in changing TCP where adding new extensions has a 
small chance of working as middle boxes will often strip things they do not 
understand, or not let the packets through. If all you can see is UDP flows it 
is harder to do much. Another benefit is faster handshake for the encrypted 
case. Proposed extensions include different algorithms for congestion control, 
for example for different environments like in datacentre or high latency 
connections, and forward error correction.

I had partly been ignoring QUIC as it has not yet been finalised, and had some 
temporary encryption included that was going to be removed to be replaced by 
TLS 1.3, and also as it seemed to be very tied up with HTTP/2. I also had some 
idea that layering encryption over TCP in possibly slightly non spec compliant
ways might make sense. But then a few weeks back, a paper on [nQUIC or Noise on 
QUIC](https://dl.acm.org/citation.cfm?id=3284854) came out, and I decided to 
take another look. It turned out that there were other people interested in 
removing the strong tie to TLS, and also looking further it seems that the 
protocol is not that tied to HTTP, and it does provide a general transport with 
multiple streams. The [IETF standard 
drafts](https://datatracker.ietf.org/doc/draft-ietf-quic-transport/) split out 
the TLS implementation, and it looks like there is interest in pushing for a 
standard Noise based version. Quic is not significantly more complex than TCP, 
especially as you can in effect hard code the number of streams if you do not 
want to use that feature, for example on an embedded system. Noise over QUIC, 
without HTTP looks pretty reasonable for small systems that have enough 
performance to do encryption and a little memory, even down to 
microcontrollers. You could even customise it for some applications in closed 
environments.

So what has this got to do with unikernels? Well the interesting thing about 
QUIC is that it always runs in userspace, not in the kernel on conventional 
systems. So that puts unikernels on an equal footing, they can use the same 
implementations as other applications use. There are [already 
implementations](https://github.com/quicwg/base-drafts/wiki/Implementations) in 
C, C++, Go, Rust, TypeScript, Objective C, Python, and no doubt more. 
Interfacing QUIC to a transport stack is pretty simple as UDP is just a thin 
layer over ethernet. There is no reason why the implementation should be any 
less efficient, indeed it can probably be made more efficient as it csn bypass 
several abstraction layers.

There are some potential issues in that some firewalls block QUIC (which is 
typically on UDP port 443); browsers will switch to TCP in that case. A QUIC 
only unikernel might not have that luxury, especially in some embedded 
situation. Larger machines can still fall back to TCP, but that can be a less 
optimised version. The main use case for QUIC would initially be for traffic 
between dedicated unikernel or embedded services, especially if you are using 
Noise rather than TLS for a very small implementation, not public endpoints. 
There are [some 
concerns](https://calendar.perfplanet.com/2018/quic-and-http-3-too-big-to-fail/)
 that the CPU overhead of QUIC is higher, so it may not be suitable for 
embedded applications, and there are no benefits over TCP for those cases. But 
there is freedom to iterate in a way that there is much less with TCP, so I 
think it is definitely worth examining. Research in whether CPU overhead is a 
necessary part of the protocol, and how to measure efficiency in different 
environments is also productive.

