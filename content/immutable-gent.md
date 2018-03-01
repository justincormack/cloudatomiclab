+++
author = "Justin Cormack"
date = "2018-02-06T20:40:00Z"
description = "Making Immutable Infrastructure simpler with LinuxKit"
tags = []
title = "Making Immutable Infrastructure simpler with LinuxKit"
+++

## Config Management Camp

I gave this talk at Config Management Camp 2018, in Gent. This is a great event, and I recommend you go if you are interested in systems and how to make them work.

Did I mention that Gent is a lovely Belgian town?

<img src="/gent.jpg" width="100%"/>

The slides [can be downloaded here](/Making Immutable Infrastructure simpler with LinuxKit.pdf).

<iframe width="560" height="315" src="https://www.youtube.com/embed/DmcSo1Wts0Q?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Below is a brief summary.

## History

Some history of the ideas behind immutability.

> “The self-modifying behavior of both manual and automatic administration techniques helps explain the difficulty and expense of maintaining high availability and security in conventionally-administered infrastructures.
A concise and reliable way to describe any arbitrary state of a disk is to describe the procedure for creating that state.”

Steve Traugott, Why Order Matters: Turing Equivalence in Automated Systems Administration, 2002

> “In the cloud, we know exactly what we want a server to be, and if we want to change that we simply terminate it and launch a new server with a new AMI.”

Netflix Building with Legos, 2011

> “As a system administrator, one of the scariest things I ever encounter is a server that’s been running for ages. If you absolutely know a system has been created via automation and never changed since the moment of creation, most of the problems disappear.”

Chad Fowler,Trash Your Servers and Burn Your Code, 2013

> “Use container-specific OSes instead of general-purpose ones to reduce attack surfaces. When using a container-specific OS, attack surfaces are typically much smaller than they would be with a general-purpose OS, so there are fewer opportunities to attack and compromise a container-specific OS.”

[NIST Application Container Security Guide, 2017](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-190.pdf)

## Updates

Updating software is a hard thing to do. Sometimes you can update a config file and send a `SIGHUP`, other times you have to kill the process. Updating a library may mean restarting everything that depends on it. If you want to change the Docker config that means restarting all the containers potentially. Usually only Erlang programs self update correctly. Our tooling has got a domain specific view of how to do all this, but it is difficult. Usually there is some downtime on a single machine. But in a distributed system we always allow for single system downtime, so why be so hardcore about updates? Just restart the machine with a new image, that is the immutable infrastructure idea. Not immutable, just disposable.

## State

Immutability does not mean there is no state. Twelve factor apps are not that interesting. Everything has data. But we have built Unix systems based on state being all kind of mixed up everywhere in the filesystem. We want to try to split between immutable code and mutable application state.

Functional programming is a useful model. There is state in functional programs, but it is always explicit not implicit. Mutable global state is the thing that functional programming was a reaction against. Control and understand your state mutation.

Immutability was something that we made people do for containers, well Docker did. LXC said treat containers like VMs, Docker said treat them as immutable. Docker had better usability and somehow we managed to get people to think they couldn't update container state dynamically and to just redeploy. Sometimes people invent tooling to update containers, with Puppet or Chef or whatever, those people are weird.

The hard problems are about distributed systems. Really hard. We can't even know what the state is. These are the interesting configuration management problems. Focus on these. Make the individual machine as simple as possible, and just think about the distribution issues. Those are really hard. You don't want configuration drift on machines messing up your system, there are plenty of ways to mess up distributed systems anyway.

## Products

Why are there no immutable system products? Actually the sales model does not work well with something that is at build time only, not running on your infrastructure. The billing models for config management products don't really work well. Immutable system tooling is likely to remain open source and community led for now. Cloud vendors may well be selling you products based on immutable infrastructure though.

## LinuxKit

[LinuxKit](https://github.com/linuxkit/linuxkit) was originally built for Docker for Mac. We needed a simple embedded, maintainable, invisible Linux host system to run Docker. The first commit message said "not required: self update: treated as immutable". This project became LinuxKit, open sourced in 2017. The only kind of related tooling is Packer, but that is much more complex. One of the goals for LinuxKit was that you should be able to build an AWS AMI from your laptop without actually booting a machine. Essentially LinuxKit is a filesystem manipulation tool, based on containers.

LinuxKit is based on a really simple model, the same as a Kubernetes pod. First a sequential series of containers runs, to set up the system state, then `containerd` runs the main services. This config corresponds to the yaml config file, which itself is used to build the filesystem. Additional tooling lets you build any kind of disk format, for EFI or BIOS, such as ISOs, disk images or initramfs. There are development tools to run images on cloud providers and locally, but you can use any tooling, such as Terraform for production workloads.

## Why are people not using immutable infrastructure?

Lack of tooling is one thing. Packer is really the only option other than LinuxKit, and it has a much more complex workflow involving booting a machine to install. This makes a CI pipeline much more complex. There are also nearly immutable distros like Container Linux, but this is very hard to customise compared to LinuxKit.

## Summary

This is a very brief summary of the talk. Please [check out LinuxKit](https://github.com/linuxkit/linuxkit) it is an easy, different and fun way to use Linux.


