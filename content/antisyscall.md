+++
author = "Justin Cormack"
date = "2017-12-23T12:40:00Z"
description = "Eleven syscalls that suck"
tags = []
title = "Eleven syscalls that suck"
+++

People ask me “Justin, what system calls suck so much, suck donkeys for breakfast, like if Donald Trump were a syscall?” Here I reveal all.

If you don't like sucky syscalls, try [the eleven non sucky ones](https://www.cloudatomiclab.com/prosyscall/).

## 0. `ioctl`

It can‘t decide if it‘s arguments are integers, strings, or some struct that is lost in the midst of time. Make up your mind! Plan 9 was invented to get rid of this.

## 1. `fcntl`

Just like `ioctl` but for some different miscellaneous operations, because one miscelleny is not enough.

## 2. `tuxcall`

Linux put a web server in the kernel! To win a benchmark contest with Microsoft! It had it‘s own syscall! My `enum tux_reactions` are YUK! Don‘t worry though, it was a distro patch (thanks Red Hat!) and never made it upstream, so only the man page and reserved number survive to taunt you and remind you that the path of the righteous is beset by premature optmization!

## 3. `io_setup`

The Linux asynchronous IO syscalls are almost entirely useless! Almost nothing works! You have to use `O_DIRECT` for a start. And then they still barely work! They have one use, benchmarking SSDs, to show what speed you could get if only there was a usable API. Want async IO in kernel? Use Windows!

## 4. `stat`, and its friends and relatives

Yes this one is useful, but can you find the data structure it uses? We have `oldstat`, `oldfstat`, `ustat`, `oldlstat`, `statfs`, `fstatfs`, `stat`, `lstat`, `fstat`, `stat64`, `lstat64`, `fstat64`, `statfs64`, `fstatfs64`, `fstatat64` for `stat`ing files and links and filesystems in Linux. A new bunch will be along soon for Y2038. Simplify your life, use a BSD, where they cleaned up the mess as they did the cooking! Linux on 32 bit platforms is just sucky in comparison, and will get worse. And don't even look at MIPS, where the padding is wrong.

## 5. Linux on MIPS

Not a syscall, a whole implemntation of the Linux ABI. Unlike the lovely clean BSDs, Linux is different on each architecture, system calls randomly take arguments in different orders, and constants have different values, and there are special syscalls. But MIPS takes the biscuit, the whole packet of biscuits. It was made to be binary compatible with old SGI machines that don't even exist, and has more syscall ABIs than I have had hot dinners. Clean it up! Make a new sane MIPS ABI and deprecate the old ones, nothing like adding another variant. So annoying I think I threw out all my MIPS machines, each different.

## 6. `inotify`, `fanotify` and friends

Linux has no fewer than three file system change notification protocols. The first, `dnotify` hopped on `ioctl`‘s sidekick `fcntl`, while the two later ones, `inotify` and `fanotify` added a bunch more syscalls. You can use any of them, and they still will not provide the notification API you want for most applications. Most people use the second one, `inotify` and curse it. Did you know `kqueue` can do this on the BSDs?

## 7. `personality`

Oozing in `personality`, but we just don't get along. Basically obsolete, as the kernel can decide what kind of system emulation to do from binaries directly, it stays around with some use cases in persuading `./configure` it is running on a 32 bit system. But it can turn off ASLR, and let the CVEs right into your system. We need less `persoanlity`!

## 8. `gettimeofday`

Still has an obsolete timezone value from an old times when people thought timezones should go all the way to the kernel. Now we know that your computer should not know. Set its clock to UTC. Do the timezones in the UI based on where the user is, not the computer. You should use `clock_gettime` now. Don't even talk to me about locales. This syscall is fast though, don't use it for benchmarking, its in the VDSO.

## 9. `splice` and `tee`

These, back in [https://lwn.net/Articles/119682/](2005) were a quite nice idea, although Linux said then "it is incomplete, the interfaces are ugly, and it will oops the system if anything goes wrong". It won't oops your system now, but usage has not taken off. The nice idea from Linus was that a pipe is just a ring buffer in the kernel, that can have a more general API and use cases for performant code, but a decade on it hasn't really worked out. It was also supposed to be a more general `sendfile`, which in many ways was the successor of that Tux web server, but I think `sendfile` is still more widely used.

## 10. `userfaultfd`

Yes, I like file descriptors. Yes [https://criu.org/Main_Page](CRIU) is kind of cool. But userspace handling page faults? Is nothing sacred? I get that you can do this badly with a `SIGSEGV` handler, but talk about lipstick on a pig.
