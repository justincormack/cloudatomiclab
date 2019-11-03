+++
author = "Justin Cormack"
date = "2019-11-01T17:27:00Z"
description = "Linearity among the toctou"
tags = []
title = "Linearity among the toctou"
+++

<img src="/lineforawalk.png" width=40%">

_Illustration from Paul Klee, Pedagogical Sketchbook, 1925_

I have been reading a lot of papers on linear types recently. Originally it was 
to understand better why Rust went down the path it did, but I found a lot more 
interesting stuff there. While some people now are familiar with linear typesas 
the basis for Rust's memory management, they have been around for a long time 
and have lots of other potential uses. In particular they are interesting for 
improving resource allocation in functional programming languages by reusing 
storage in place where possible. Generally they are useful for reasoning about 
resource allocation. While the Rust implementation is probably the most widely 
used at present, it kind of obscures the underlying simple principles by adding 
borrowing, so I will only mention it a little in this post.

So what are linear types? I recommend you read [“Use-once” variables and linear 
objects: storage management, reflection and 
multi-threading](https://dl.acm.org/citation.cfm?doid=199818.199860) by Henry 
Baker, as it is the best general overview I have found. The basic idea is 
extremely simple, linear variables can only be used once, so any function that 
receives one must either return it, or pass it to another function that 
consumes it. Using values only once sounds kind of weird and restrictive, but 
there are some ways it can be made easier. Some linear types may have an 
explicit copy operation to duplicate them, and others may have operations that 
return a new value, in a sequential way. For example a file object might have a 
read operation that returns the portion read and a new linear object to read 
for the next part, preserving a functional model: side effects are fine if you 
cannot reuse a variable. You won't really recognise much of the Rust model 
here, as it allows borrows, which presents a much less austere effect. It does 
all sound fairly odd until you get used to it, even though it is simpler than 
say monads as a way of sequencing. Note also that there are related affine 
types,where you can use values zero or one times, so values can be discarded, 
and other forms such as uniqueness types, and many other fun variants in the 
literature.

Memory is probably the easiest way to understand the use cases. Think about 
variables as referring to a chunk of memory, rather than being a pointer. 
Memory can be copied, but it is an explicit relatively costly operation (ie 
`memcpy`) on the memory type, so the normal access should be linear with 
explicit copying only if needed. Because the value of the memory may be changed 
at any time by a write, you need to make sure there are not multiple writers or 
readers that are not reading in a deterministic order. Rust does this with 
mutable borrows, and C++ has a related thing with move semantics.

Rust's borrow checker allows either a single reference with read and write 
access, or multiple readers when there is no write access. Multiple readers is 
of course not a linear access pattern, but is safe as multiple reads of an 
immutable object return the same value. The complexity of the borrow checker 
comes from the fact that objects can change between these states, which 
requires making sure statically that all the borrows have finished. Some of the 
use cases for linearity in functional languages relate to this, such as 
efficiently initialising an object that will be immutable later, so you want 
linear write access in the initialisation phase, followed by a non linear read 
phase. There are definitely interesting language tradeoffs in how to expose 
these types of properties.

Anyway, I was thinking about inter process communication (IPC) again recently, 
in particular ring buffer communication between processes, and it occured to me 
that this is another area where linearity is a useful tool. One of the problems 
with shared memory buffers for communication, where one process has read access 
and the other write access for each direction of communication is that the 
writing process may try to attack the reader by continuing to write after 
reading has started. The same issue applies for userspace to kernel 
communication, where another userspace thread may write to a buffer that the 
kernel has already read. This is to trigger a time of check time of use 
(toctou) attack, for example if there is a check that a size is in range, but 
after that the attacker increases it. The standard defence is to copy buffers 
to a private buffer, where validation may happen undisturbed. This of course 
has a performance hit, but many IPC implementations, and the Linux kernel, do 
this for security reasons.

Thinking about toctou as a linearity problem, we can see that "time of check" 
and "time of use" are two different reads, and if we treat the read buffer as a 
linear object, and require that its contents are each only read once, then time 
of check and time of use cannot be different. Note of course that it does not 
matter exactly which version gets read, all that matters is that it is a 
consistent one. We have to remember the value of the part we check and keep 
that for later if we can't use it immediately. So linear read has its uses. Of 
course it is not something that programming languages give us at present, 
generally a compiler will assume that it can reload from memory if it needs to. 
Which is why copying is used; copying is a simple linear operation that is 
available. But there are often cases where the work being done on the buffer 
can be done in a linear way without copying, if only we had a way of telling 
the compiler or expressing it in the language.

Overall, I have found the linear types literature helpful in finding ways to 
think about resource allocation, and I would recommend exploring in this space.
