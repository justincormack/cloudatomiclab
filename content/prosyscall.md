+++
author = "Justin Cormack"
date = "2017-12-23T12:40:00Z"
description = "Eleven syscalls that rock the world"
tags = []
title = "Eleven syscalls that rock the world"
+++

People ask me “Justin, what are your favourite system calls, that you look forward to their return codes?” Here I reveal all.

If you are in a bad mood you might prefer [sucky syscalls that should never have existed](https://www.cloudatomiclab.com/antisyscall/).

## 0. `read`

You cannot go wrong with a `read`. You can barely `EFAULT` it! On Linux amd64 it is syscall zero. If all its arguments are zero it returns zero. Cool!

## 1. `pipe`

The society for the preservation of historic calling conventions is very fond of `pipe`, as in many operating systems and architectures it preserves the fun feature of returning both of the file descriptors as return values. At least Linux MIPS does, and NetBSD does even on x86 and amd64. Multiple return values are making a comeback in languages like Lua and Go, but C has always had a bit of a funny thing about them, but they have long been supported in many calling conventions, so let us use them in syscalls! Well, one syscall.

## 2. `kqueue`

When the world went all C10K on our ass, and scaleable polling was a thing, Linux went `epoll`, the BSDs went `kqueue` and Solaris went `/dev/poll`. The nicest interface was `kqueue`, while `epoll` is some mix of edge and level triggered semantics and design errors so bugs are still being found.

## 3. `unshare`

Sounds like a selfish syscall, but this generous syscall call is the basis of Linux namespaces, allowing a process to isolate its resources. Containers are built from `unshare`s.

## 4. `setns`

If you liked `unshare`, its younger but cooler friend takes file descriptors for namespaces. Pass it down a unix socket to another process, or stash it for later, and do that namespace switching. All the best system calls take file descriptors.

## 5. `execveat`

Despite its somewhat confusing name (FreeBSD has the saner `fexecve`, but other BSDs do not have support last time I checked), this syscall finally lets you execute a program just given a file descriptor for the file. I say finally, as Linux only implemented this in 3.19, which means it is hard to rely on it (yeah, stop using those stupid old kernels folks). Before that Glibc had a terrible userspace implementation that is basically useless. Perfect for creating sandboxes, as you can sandbox a program into a filesystem with nothing at all in, or with a totally controlled tree, by opening the file to execute before `chroot` or changing the namespace.

## 6. `pdfork`

Too cool for Linux, you have to head out to FreeBSD for this one. Like fork, but you get a file descriptor for the process not a pid. Then you can throw it in the `kqueue` or send it to another process. Once you have tried process descriptors you will never go back.

## 7. `signalfd`

You might detect a theme here, but if you have ever written traditional 1980s style signal handlers you know how much they suck. How about turning your signals into messages that you can read on, you guessed it, file descriptors. Like, usable.

## 8. `wstat`

This one is from Plan 9. It does the opposite of `stat` and writes the same structure. Simples. Avoids having `chmod`, `chown`, `rename`, `utime` and so on, by the simple expedient of making the syscall symmetric. Why not?

## 9. `clonefile`

The only cool syscall on OSX, and only supported on the new APFS filesystem. Copies whole files or directories on a single syscall using copy on write for all the data. Look on my works, `copy_file_range` and despair.

## 10. `pledge`

The little sandbox that worked. OpenBSD only here, they managed to make a simple sandbox that was practical for real programs, like the base OpenBSD system. Capsicum form FreeBSD (and promised for Linux for years but no sign) is a lovely design, and gave us `pdfork`, but its still kind of difficult and intrusive to implement. Linux has, well, `seccomp`, LSMs, and still nothing that usable for the average program.
