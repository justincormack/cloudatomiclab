+++
author = "Justin Cormack"
date = "2019-01-27T19:00:00Z"
description = "Kubernetes as an API standard"
tags = []
title = "Kubernetes as an API standard"
+++

There was a lot of interest in my tweet a couple of months about writing an 
implementation of the Kubernetes API in Rust. I had a good conversation at 
Kubecon with some people about it, and thought I should explain more why it is 
interesting.

<blockquote class="twitter-tweet" data-lang="en-gb"><p lang="en" dir="ltr">So, 
it seems the competition is on. Most complete implementation of the Kubernetes 
API in Rust by May 20 2019 (kubecon EU). Prize details forthcoming. Tiebreaker 
is smallest codebase.</p>&mdash; Justin Cormack (@justincormack) <a 
href="https://twitter.com/justincormack/status/1067185866001575936?ref_src=twsrc
%5Etfw">26 November 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" 
charset="utf-8"></script>

Kubernetes is an excellent API for running code reliably. So much so that 
people want to run it everywhere. People have described it as the universal 
distributred systems API, and something that will eventually be embedded into 
hardware, or the kernel (or Linux) of distributed systems. Maybe some of these 
are ambitious, but nothing wrong with ambition, and hey it is a nice, simple 
API at its core. Essentially it just does reconciliation between the world and 
desired state for an extensible set of things, things that include a concept of 
a pod by default. That is pretty much it, a simple idea.

A simple idea, but not simply expressed. If you build a standalone Kubernetes 
system, somehow that simple idea amounts to a gigabyte of compiled code. Sure, 
there are some extraneous debug symbols, and a few extra versions of etcd for 
version upgrades, and maybe one day Go will produce less bloated code, but that 
is not going to cut it for embedded systems and other interesting potential use 
cases of Kubernetes. Nor is it easy to understand, find your way around the 
code and hack on it.

Another problem with Kubernetes is that it suffers from the problem that the 
implementation is the specification. Lots of projects start like that but as 
they mature the specification is often separated, and alternative 
implementations can thrive. Without an independent specification, alternative 
implementations often have to copy every accidental nuance of the original, and 
even replicate bugs. Kubernetes is in the right state where starting to move 
towards an independent specification would be productive. We know that there 
are some rough edges in the implementation that need to be cleared up, and some 
parts where the API is not yet the best it could be.

One approach is to try to cut back the current implementation to a more 
manageable size, by removing parts. This is what Darren Shepherd of Rancher has 
done with ["k3s"](https://github.com/ibuildthecloud/k3s), removing a million or 
so lines of code. But a second, complementary approach is to build a new simple 
implementation from the ground up without any baggage to start with. Then by 
looking at differences in behaviour, you can start to understand which parts 
are the core specification, and which parts are accidental. Given that the way 
the code for Kubernetes is written has been described as a "clusterfuck" by 
[Kris Nova](https://fosdem.org/2019/schedule/event/kubernetesclusterfuck/), 
this seems a productive route: "Unknown to most, Kubernetes was originally 
written in Java... If the anti patterns weren’t enough we also observe how 
Kubernetes has over 20 main() functions in a monolithic “build” directory... 
Kubernetes successfully made vendoring even more challenging than it already 
was, and discuss the pitfalls with this design. We look at what it would take 
to begin undoing the spaghetti code that is the various Kubernetes binaries."

Of course we could write a new implementation in Go, but the temptation would 
then be to import bunches of existing code, and it might not end up that 
different. A different language makes sense to stop that. The aim should be to 
build the minimum needed to implement the code API. So what language? Rust 
makes the most sense it seems, although there are some other options.

There is a small but growing community of cloud native Rust projects. In the 
CNCF, there is [TiKV](https://github.com/tikv/tikv) from PingCAP and [the 
Linkerd 2 data plane](https://github.com/linkerd/linkerd2-proxy). Anther 
project that has recently been launched in the space is [AWS 
Firecracker](https://github.com/firecracker-microvm/firecracker). The Rust 
ecosystem is especially strong in security, and control of memory usage, both 
of which are important for effective scalable systems. In the last year or so 
the core libraries needed in the cloud native space have really been filled in.

So are you interested in hacking on a greenfield implementation of Kubernetes 
in Rust? There is not yet a public codebase to hack on, but I know that there 
are some people hacking in private. The minimal viable project is something 
that you can talk to with kubectl and run pods, and API extensions. The 
conformance tests should help, although they are not complete enough to 
constitute a specification by any means, but starting to pass some tests would 
be a satisfying achievement. If you want to meet up with cloud native Rust 
community, a bunch of people will be at [Fosdem](https://www.fosdem.org) in 
early February, and I will sort out a fringe even at KubeCon EU as well. Happy 
hacking!



