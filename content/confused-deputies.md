+++
author = "Justin Cormack"
date = "2018-12-27T19:00:00Z"
description = "Confused Deputies Strike Back"
tags = []
title = "Confused Deputies Strike Back"
+++

A few weeks back Kubernetes had its first really severe security issue, 
[CVE-2018-1002105](https://github.com/kubernetes/kubernetes/issues/71411).
For some background on this, and how it was discovered, I recommend [Darren 
Shepherd's blog post](https://rancher.com/blog/2018/2018-12-04-k8s-cve/), he 
discovered it via some side effects and initially it did not appear to be a 
security issue just an error handling issue. Of course we know well that many 
error handling issues can be escalated, but why was this one so bad?

To summarize the problem, there is an API server proxy component, that clients 
can use to talk to other API endpoints. As the [postmortem 
document](https://github.com/kubernetes/kubernetes/files/2700818/PM-CVE-2018-100
2105.pdf) says

- Kubernetes API server proxy components still use http/1.1 upgrade-based 
connection tunneling, which does not distinguish between request data sent by 
the apiserver while establishing the backend connection, and data sent by the 
requesting user

- High and low-privilege API requests to aggregated API servers are proxied via 
the same component with the same high-permission transport credentials

Well, this security issue is actually well known enough to have its own name, 
it is the [confused deputy 
problem](https://en.wikipedia.org/wiki/Confused_deputy_problem), originally 
written about by [Norm Hardy in 
1988](http://zoo.cs.yale.edu/classes/cs422/2010/bib/hardy88confused.pdf) 
although referring to an original example from the 1970s. The essence of the 
problem is that there are three parties involved, a user, a proxy or deputy 
type component and an object or service that needs to be accessed, or a similar 
set of endpoints. The user connects to the deputy to perform an action on an 
object, but the deputy can be persuaded to act on an object that it has access 
to rather than one the end user has access to.

Imagine asking your accountant to fill in your tax return. Your accountant has 
access to your tax return, but also to those of other customers. If the 
accountant is buggy or can be confused she could fill in one of these tax 
returns instead of yours. The general problem is that in order to run a tax 
return filing service, you need the ability to fill in lots of different 
people's tax returns. You become a very privileged node, a superuser of tax 
returns. The tax office has to respect your authority to fill in lots of tax 
returns, and read them, so the accountant's credentials must be very 
privileged. We see similar designs in all sorts of places, like suid 
applications in Unix that can do operations on behalf of any user and must be 
very highly trusted, and are often the source of security bugs.

What is the solution? Well we can not have these deputies. Fill in your own tax 
return! But in effect this says do not use microservices. If every endpoint 
needs to have the code for filling in tax returns we lose the benefit of 
microservices, we have to update lots of endpoints together, we cannot have a 
team building better accountant services and so on. What we really want is that 
the accountant does not have to be a superuser, but instead she has no 
permissions on her own but we can pass credentials (maybe time limited) to 
update our tax return (but not to generally impersonate us) with our request. 
This access control model is called [capability-based 
security](https://en.wikipedia.org/wiki/Capability-based_security): access is 
granted via unforgeable but transferable tokens that provide access to objects. 
You can imagine they are keys, like passing your car key to a valet service, 
rather than the valet service having a master key for all cars that they might 
need to park.

The standard access control list (ACL) models of authorization are all about 
making decisions based on identity, a concept that clearly must not be 
transferable. I never want my accountant to have to (or be able to) pretend to 
be me to fill in my tax return. The classic solution in this case would be for 
me to be able to add additional people to the ACL for my tax return; this is 
modeled in new ACL frameworks like NGAC from NIST (sorry no link right now the 
website is down due to the government shutdown). This does not immediately seem 
applicable to the Kubernetes issue though, and is much more complex than 
passing my API access credential to the API proxy server. At this point I 
highly recommend the excellent short paper [ACLs don’t by Tyler 
Close](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.406.4684&rep=rep
1&type=pdf), one of my favourite papers (I should do a papers we love session 
on it). His examples mainly come from the browser, another prevalent deputy 
with a lot of security issues, such as CSRF another confused deputy attack. 
Capabilities are actually very simple to understand and reason about.

ACL based security is fine for many situations, in particular where there are 
only two parties and you just want to mediate access to a set of resources. But 
microservices do not appear to be in that sweet spot, as Kubernetes found out 
with its API proxy microservice. Bugs can be fixed, but as the retrospective 
points out all changes will need to be examined for security issues. As Tyler 
Treat says "the correct implementation of an access policy cannot be 
ascertained by an examination of the ACLs configured for an application, but 
must also include an examination of the program’s source code. To date, this 
technique has been error prone." It was not even the only bug that week that 
was a confused deputy issue, the [Zoom critical 
bug](https://medium.com/tenable-techblog/remotely-exploiting-zoom-meetings-5a811
342ba1d) was the same issue, where UDP packets could confuse the deputy 
service. These are critical issues happening on a regular basis, and no doubt 
many more lurk.

The entire reason for microservices is to have third parties to delegate 
services to, and we need to shift away from ACL based models to capabilities 
for microservices. Of course this is non trivial, distributed capabilities (as 
opposed to local ones) have not been used much and we don't have a good 
infrastructure for them yet. I will write more about practicalities in a 
further post, but we need to start shifting security to be microservice native 
too not just adopting things that worked for monoliths.
