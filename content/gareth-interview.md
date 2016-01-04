+++
author = "Justin Cormack"
date = "2016-01-02T19:03:59Z"
description = "Interview with Gareth Rushgrove"
tags = []
title = "Interview with Gareth Rushgrove"

+++

## Puppet: Configuration Management in the age of containers

Interview with Gareth Rushgrove of Puppet Labs, 30 April 2015, lightly edited transcript

<em>This interview was originally for [Docker in Production](http://shop.oreilly.com/product/9781939902184.do) but did not get into the final version</em>

There are three areas of configuration management that you might want to apply tools to, the host that manages containers, the containers themselves and the overall system. Tools can be applied independently to each area.

For building containers the tools work but they are not optimised yet, and the Docker build API is horrendous. Docker file is not a client, it is the wire format, so all tools that want to do a better job of building things must compile down to a Docker file which is limiting. We would like more direct integration.

It is also important to split up what configuration management is. It is not just tools, it is a discipline for managing your configuration. Some configuration is upfront, it is modelling, a mental model to share with your team and you want to ensure it is correct. There is also emerging configuration, runtime configuration. I have been describing this as two speeds. The modelling stuff changes infrequently, weeks months or hours, but the runtime stuff like a new machine being added is very fast. So it is important to think of it as a discipline not just as tools.

Containers are different, so people are learning new stuff, and are thinking more about dynamic systems. Looking at something new allows people to think about things in a new way. And smaller services, and more things mean there are more changes across the system, having small things allows more changes with less risk. The culture is about changing things faster while minimising risks.

Such as service discovery, which is part of configuration management. Service discovery moves configuration from modelling to emergent properties of the system. This changes how configuration is distributed. Your mental model changes from five servers connected to a load balancer to a pool of hosts, away from a fixed property.

Where there are finite resources, you have been given two servers or 20 IP addresses, you want an explicit model of where they are all the time. Self service infrastructure removes those constraints. People have used tools to model emergent properties, but you end up with a lot of complexity.

Puppet sees things as a graph, understanding how instances relate to security groups is similar to how a group of files and packages relate on a host. There is an onion of infrastructure, physical machines, applications, services spanning multiple hosts, layers of the onion.

Defining the primitives is useful. Some of the work we are doing around Kubernetes and Mesos is about defining the high level primitives once you stop configuring individual hosts. The more hosts you have the less configuring files and packages on hosts scales. After 100 it gets progressively harder and the rate we add machines outstrips the ability. We need to give power tools to support that and fix emerging problems. Need to move from configuring hundreds or thousands of individual hosts, and the race is on now to decide what the primitives are for configuring these systems.

The public cloud has been interesting, as all the vendors resisted standardisation. They all have a machine or VM, but when you come to networking the primitives are really different, and to do real network you have to drop down to the native APIs and more than that the primitives and models are different. You can't abstract over them without going to lowest common denominator because the primitives are different. Networking is still a killer where the primitives and APIs are still different.

Currently with Puppet there is support for networking. There is support for switches like F5 or Juniper, and support for the cloud providers, but with different models. This lets you build the layers of configuration you need. People are exposing existing devices to software configuration, and standardising interfaces.

Puppet comes with a bunch of existing providers for files, packages and users out of the box, but that is really an implementation detail, packaging. Puppet could have been shipped without any of them out of the box. The interesting question is in twenty years time are declarative modelling languages still useful but no one is even bothered about individual users or files then Puppet won't ship those in core, they will just be available as a module. Whereas today support for EC2 is shipped as a module, and there are modules to configure [Chronos](https://github.com/mesos/chronos), which is not a host thing but sits across a bunch of machines. I think we will see way more resources like that.

The moment you are running a distributed protocol that is software and that needs to be configured and consistency is important and must be maintained. Even where there are new primitives like replicated applications the software and the usage of that needs configuration.

There is still a need for something that says these are the services I want to run and this is how they are configured. Moving these to containers does not change this massively. It makes a bunch of things easier. A container is a unit of a package and a service format, you need not think of those separately any more.

If you go to an organization that has been around for six months and has more than ten people, they will be running a huge amount of software, the messy real world. It is nice to think you can have one operating system and one version and one way of doing things. But the older organization has a bit of everything and this is not going to change and they are not going to get rid of stuff.

This has to do with financial and business constraints not operational constraints. If things are working, things are not going to move, and this will always be the case.

The messes with containers will be predictable, nothing novel or new. Docker is interesting as it has a really compelling developer story it does not yet have a really compelling operations story. That's not that it won't do but it is new. There is loads of focus on the technical operations side, getting the tech right, what do the operational models look like, how do you segregate things. In a multi tenant environment now you wrap containers in virtualisation. The abstraction above the physical is the easy bit but sometimes you care about things not being on the same host or network. Cloud Foundry did some work but it was more or less are you on inside or outside.

Google has one massive workload for search, but it is all about search, if you have access to one part you have access to the rest, so segregation does not matter so much. When you have persistent workloads that are very diverse I think thats where there is a disconnect between a brand new startup and a larger organization. If you have multiple microservices serving one application, segregation is not so important. The workload is not that diverse, and your internally services like HR are all outsourced. Large organizations will have independent things that are running on single machines. Wrapping this in containers does not make sense now. The more diverse the workload, the more you end up with many security domains, and the tools for reasoning about that are not there yet. The one big flat network is a failing of a naive developer mindset; not the developers are naive, the environments they are in do not have those problems.

Security was largely about a trust in hardware. Once everything is software the guarantees go away, and this changes the model. You have layers, model once and apply twice. People are not yet doing this, but you could. Testing what goes across the wire for example, by having a proxy container that sits between services and makes sure they are doing the right thing.

For example if services are supposed to be talking JSON, check if it is JSON and it meets the schema and having that implemented as a separate thing, potentially in a separate security domain. There are a lot of practises that don't exist yet. Putting your tests into production, putting your tests in production, if it ever fails something has gone badly wrong and it should fail. Build tools so people do not have to trust things, but it is not common practise except at high end. Containers make it worse as you have more moving parts, potential to screw up configuration is higher.

The intersection between configuration management and monitoring is also important, seeing that the defined configuration is working. The model of the configuration drives the model of the monitoring system, so you have feedback into the configuration. It is not quite a closed loop yet, and monitoring tools do not have much context, but in theory you could put all these things together. The widespread adoption of these tools is still happening, and integration is slow as there is not one winner in terms of tools. With databases there are a few, but there are so many tools that there is not the same level of integration as there are so many options.

The commercial vendors are putting these stacks together into more opinionated layers, for example Tectonic from CoreOS is a bundle of CoreOS and Kubernetes and so on and you cannot change the pieces, the way they fit together is defined and opinionated.

There is a tension between what paid customers want, highly opinionated ways to do things, and the open source community which wants many small independent things that do one thing well that they can tie together how they want. With lots of open source products like Docker being funded by companies this is going to be interesting. It is not possible to square that.

Puppet will end up shipping more opinionated infrastructure supporting containers to large enterprises, and what we are building is the start of an opinionated workflow. Only a small number of our clients are using containers so far, we ship opinionated things are more do. The best practises around containers have not been worked out yet. The high level tools are not finished yet, nor is the networking layer.

Commercial interests mean there will be multiple flavours with different models, we are not going to adopt the same thing, from a technology and user point of view it would be lovely. From a business point of view it will not happen, there will be multiple platforms; some of the reasons are good, we don't know what all the answers are yet. The fact you can compose tools in different ways does not mean it is easy or cheap.

If you are moving fast being able to change the components fast is really important. Doing this on the service level is a massive advantage when you have small growing teams. There are other benefits too. Will there be a market for microservices? Will SAP ship as 139 individual components that you can in theory replace? I don't think we know yet what it will look like yet. From a consumer point of view I want one thing for one price. Containers as a unit of shipping certain types of software make sense, especially when more widely adopted. Appliance like containers are where enterprises will be interested. Both Docker and Red Hat are interested. Once there is a container runtime everywhere the installer will just find a bunch of containers, and this removes a bunch of work around packaging. But at this point it is an implementation detail. The container runtime is a given at some point, whether it is high level like Kubernetes or slightly lower level like Mesos or like Swarm or Windows containers; the whole multi host stuff is still not determined.

With Windows there is a certain amount of catchup. Server 2016 is shipping with containers out of the box, as is Hyper-V, with Docker as the interface. Containers on Windows will probably be technically better than on Linux, from what they have published so far. Containers are not going to be portable though between Windows and Linux. Windows has been somewhat irrelevant in this space but it is changing.

