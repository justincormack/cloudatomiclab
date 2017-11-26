+++
author = "Justin Cormack"
date = "2017-12-01T08:52:00Z"
description = "So why not use hardware mechanisms?"
tags = []
title = "Thinking about hardware support"
+++

# Why not use hardware mechanisms?

I made a passing comment on twitter a while back suggesting that hardware virtualisation was not the right solution for some problem and was asked if my views have changed. I thought about it and realised that they had been changing for a while. There were a bunch of things that I go into here that affected that.

One of the earliest things was a discussion some years back in the Snabb community. They are developing software for handling 10Gb+ ethernet traffic. Applications are things like routing, tunneling and so on. One of the things that is a common assumption is that you have to use the offload facilities that network cards provide to get good performance. The issue came up around checksum offload. Pretty much all network hardware provides checksum offload, but it is only for the common cases, generally TCP, UDP with IPv4 and IPv6. If you are doing something different, such as a network tunnel, then support is very rare or only available for some tunnel protocols. So you need to write code for checksums in software anyway, and it will be your performance bottleneck in the case when hardware support does not work, so it needs to be optimised. So you optimise it with SSE and AVX2, and it is really fast. Especially as you have made sure that all the data is in cache at this point, because that is the only way you can do effective line rate processing at those speeds anyway. So you remove the code to use hardware offload, and are left with an absolutely consistent performance in all cases, with no hardware dependency, simpler code as there is only a single code path regardless, and the ability to do complex nested sets of checksums where necessary.

The lessons here are

1. You have to write the software fallback anyway, and write it efficiently. So hardware support does not remove need to write code.
2. If you always use the code, you will optimise (and debug) it better than if it is just a fallback that is often not tested.
3. Hardware covers common cases, but people use things in more complex ways.

There are other lessons from the Snabb experience about hardware support too, such as the terrible performance of VF switching in much hardware. Is poor performance in hardware for certain features a bug, or not? Are we expecting too much of hardware, this was once an ethernet card and now we expect it to route between VMs, so it is not surprising the first generation does not work very well. Again we get to the point where we have to implement functions purely in software and only sometimes use the hardware support, which adds this extra complexity.

## Is hardware even hardware?

It turns out that many recent TPM "chips" are emulated by the platform management firmware on recent Intel platforms. Given that this firmware has been shown to be totally insecure, do you have any reason to trust it? Most "hardware" features are largely firmware, closed source, impossible to debug or fix, and quite likely with security issues that are hard to find. Or backdoors. Hardware may just be software you can't update.

## Availability

Another factor is how the compute market makes hardware available. Cloud providers use a bunch of hardware mechanisms, especially virtualisation, which means that these mechanisms are no longer available to the users. The sort of size you can purchase is specified by the vendor. This was one of the issues with unikernel adoption, as the sizes of VMs provided by cloud vendors were generally too large for compact unikernel applications, and the smaller ones are also pretty terrible in terms of noisy neighbour effects and so on. You cannot run your own VM in the cloud. Now some nested virtualisation is starting to appear on GCP and Azure, but it is not clear the security properties of this, the performance is worse, and it is not always available and may be buggy. Obviously there are bare metal providers like Packet (and as of this week AWS) but they are currently a small part of the market.

Other hardware devices that you might like to use are similarly unavailable. If you want a hardware device to store security keys on AWS (attached over the network) it will cost you XXXX a month, as this is a premium feature.

## Intel SGX

I was really optimistic about Intel SGX a year or so ago. It could be provided on clouds as well as on your own hardware (Azure has recently started to make it available), and could provide a similar sort of isolation to VMs, but with the advantage that the host cannot access the SGX contents at all. However there are both serious security concerns that data can be exfiltrated by side channel attacks, and Intel persists with a totally unusable model where they have to issue keys for every SGX application, which makes the whole thing so cumbersome as to be unrealistic for real applications. It is simpler to just rent a small dedicated machine for the sort of application in question, things like key management. A cynical view is that the main aim now is to generate lock-in for the Intel platform.

## Hypervisor security lessons

Hypervisors are probably the most widely used hardware isolation mechanism, and what we have learned from them is that the vast majority of the security issues are in the surrounding software. Yes, QEMU, we are looking at you. Because communication is needed over the hypervisor boundary, and for compatibility and performance reasons that communication has been a pretty complex bit of code, it has had a very large number of bugs. In addition the OS as hypervisor model has seemingly won over the microkernel as hypervisor type model in the mass market at present, for convenience reasons. The large scale cloud providers do not use the open source hypervisors in any form that is close to what is shipped in open source. If you want to run a secure hypervisor you should be looking at CrosVM, Solo5, SEL4, Muen not the generic KVM+QEMU type model. A lot of the more complex enhancements, like memory de-duplication, have been shown to have security issues (eg side channel attacks). Defence in depth is very important - if you were to run KVM on Linux for example you should look at what CrosVM has done for isolation of the implementation: coding in Rust, seccomp, no Qemu, no emulation hardware, privilege separation etc.

## Alternatives

Much of the most secure software does not use any hardware features at all. The Chromium and ChromeOS models are very much worth studying. Only recently is ChromeOS considering VMs as a sandboxing device for untrusted code (using CrosVM, not shipping yet). The browser is a difficult thing to protect compared to most server software as it executes arbitrary attacker supplied code, displays complex externally provided media, and implements complex specifications not designed with security in mind. It runs on off the shelf non hardened operating systems in general. It is perhaps not surprising that many of the recent innovation in secure software sandboxing have come from this environment. Things like NaCl, Web Assembly, Rust and so on all originate here.

Linux itself has introduced a fairly limited runtime, eBPF, that is considered safe enough to run even in a kernel context, using compiler technology to check that the code is secure and terminates. This type of technology is getting much more mature, and could be used more widely in userspace too.

Arguably the crutch of "VMs are safe" has meant that hardening Linux has had a lower importance. Or indeed software protection in general, which outside the browser case has had little attention. Java was meant to be safe code when it was introduced, but the libraries in particular turned out to have lots of interesting bugs that meant escapes were possible. Native Client was the next main step, which saw some use outside the browser context, eg in ZeroVM. Web Assembly is the major target now. There needs to be more here, as for example the current state of the art for serverless platforms is to wrap the user process in a container in a VM, which is very high overhead, and tooling for building trusted safe languages is becoming more necessary for embedding applications. Of course this is not helped by the poor overall security of Linux, which is not strongly hardened and largely driven by features not security, and which has an enormous attack surface that is very hard to cut back.

If we didn't have a (misplaced) trust in hardware security, we would be forced to build better software only security. We could then combine that with hardware security mechanisms for defence in depth. Right now we mostly have too few choices.
