+++
author = "Justin Cormack"
date = "2018-07-08T22:21:00Z"
description = "Post CRUD files"
tags = []
title = "From Filesystems to CRUD and Beyond"
+++

# From Filesystems to CRUD and Beyond

In the beginning there was the filesystem. Well, in 1965 to be precise, when 
[Multics introduced the first 
filesystem](http://www.multicians.org/fjcc4.html). The Multics filesystem was 
then implemented in Unix, and is pretty recognisable with a few tweaks with 
what we use as filesystems now. Files are byte addressed, with `read`, `write` 
and `seek` operations, and there are read, write and execute permissions by 
user. Multics had permissions via a list of users rather than groups, but the 
basic structure is similar.

We have got pretty used to filesystems, and they are very convenient, but they 
are problematic for distributed and high performace systems. In particular, as 
standardised by Posix, there are severe performance problems. A `write()` is 
required to write data before it returns, even in a situation where multiple 
writers are writing to the same file; the write should then be visible to all 
other readers. This is relatively easy to organize on a single machine, but it 
requires a lot of synchronisation on a cluster. In addition there are lots of 
metadata operations, around access control, timestamps that are technically 
required, although access time is often disabled, and directory operations are 
complex.

The first well known alternative to the Posix filesystem was probably Amazon 
S3, launched in 2006. This removed a lot of the problematic Posix constraints. 
First there are no directories, although there is a prefix based listing that 
can be used to give an impression of directories. Second, files can only be 
updated atomically as a whole. This makes it essentially a key value store with 
a listing function, and events. Later optional versioning was added too, so 
previous versions of a value could be retrieved. Access control is a rather 
complex extended version of per user ACLs, read, write and ability to make 
changes. S3 is the most succesful and largest distributed filesystem ever 
created. People rarely complain that it is not Posix compatible; atomic file 
update actually seems to capture most use cases. Perhaps the most common 
complaint is inability to append, as people are not used to the model of 
treating a set of files as a log rather than an individual appended file. There 
are interfaces to treat S3 as a Posix-like filesystem, such as via Fuse, 
although they rarely attempt to emulate full semantics and may do a lot of 
copying behind the scenes, they can be convenient for some use cases where 
users like to have a familiar interface.

One of the reasons for the match between S3 and programs was that it was 
designed around the HTTP verbs: `GET`, `PUT`, `DELETE`. The HTTP resource 
model, REST, was [documented by 
Fielding](https://www.ics.uci.edu/~fielding/pubs/dissertation/fielding_dissertation.pdf)
in 2000, before S3, and `PATCH`, which you can argue has slightly more Posixy
semantics, was only added in 2010. S3 is an HTTP service, and feels web native.
This was the same move that led to Ruby on Rails in 2005, and the growth of the
CRUD (create, read, update, delete) application as a design pattern, even if
that was originally typically database backed.

So, we went from the Multics model to a key value model for scale out, while 
keeping access control unchanged? Is this all you need to know about files? 
Well actually no, there are two other important shifts, that remain less well 
understood.

The first of these is exemplified by Git, released in 2005, although the model 
had been around before that. The core part of git is a content addressed store. 
I quite like the term "value store" for these; essentially they are just like a 
key value store only you do not get to choose the key, usually it is some sort 
of hash of the content, SHA1 (for now) in Git, SHA256 for Docker images and 
most modern versions. Often this is implemented by a key value store, but you 
can optimise, as keys are always the same size and more uniformly distributed. 
Listing is not a basic operation either. The user interface for content 
addressed stores is much simpler, of the CRUD operations, only create and read 
are meaningful. Delete is usually a garbage collection operation, and there is 
no update. From the distributed system point of view there is no longer any 
need to deal with update races, ETags and so on, removing a lot of complexity. 
The content provides the key so there are no clashes. Many applications will 
use a small key valuestore as well, such as for naming tags, but this is a very 
small part of the overall system.

Sadly, content addressible systems are not as common as they should be. Docker 
image layers were originally not content addressed, but this was switched for 
security reasons a long time ago. There are libraries such as 
[Irmin](https://github.com/mirage/irmin) that present a git model for data 
structures, 
but CRUD based models still dominate developer mindshare. This is despite the 
advantages for things like caches, where content addressed data can have an 
infinite cache lifetime, and the greater ease of distribution. It is now 
possible to build a content addressed system on S3 based on SHA256 hashes, as 
signed URLs, now that S3 supports an `x-amz-content-sha256` header, see [this 
gist for example 
code](https://gist.github.com/justincormack/496500a5bb1b0dd31f12f49b81ff931a). 
The other cloud providers, and Minio, currently still only support MD5 based 
content hashes, or CRC32c in the case of Google, that are of no use at all. 
Hopefully they will update this to a modern useful content hash soon. I would 
highly recommend looking at whether you can build the largest part of systems 
on a content addressed store.

The second big change is even less common so far, but it starts to follow on 
from the first. Access control via ACL is complicated and easy to make mistakes 
with. With content addressed storage, in situations where access control is not 
uniform, such as private images on Docker hub, access control is complicated. 
Ownership is also complicated as many people could have access to some pieces 
of content. The effective solution here is to encrypt content and use key 
management for access control. Encryption as an access control method has 
upsides and downsides. It simplifies the common read path, as no access control 
is needed on the read side at all. On the write side, with content addressing, 
you just need a minimal level of access control to stop spam. On the downside 
there is key management to deal with, and a possible performance hit. Note the 
cloud providers provide server side encryption APIs, so they will encrypt the 
contents of your S3 buckets with keys that they have access to and which you 
can use IAM to delegate, but this is somewhat pointless, as you still have 
exactly the same IAM access controls, and no end to end encryption; it is 
mainly a checkbox for people who think it fixes regulatory requirements.

So in summary, don't use filesystems for large distributed systems, keep them 
local to one machine. See if you can design your systems based on content 
addressing, which scales best, and failing that use a key value store. User ACL 
based access control is complicated to manage to scale, although cloud 
providers like it as it gives them lock in. Consider encrypting data that needs 
to be private as an access control model instead.
