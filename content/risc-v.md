+++
author = "Justin Cormack"
date = "2019-01-01T18:00:00Z"
description = "Why RISC-V?"
tags = []
title = "Why RISC-V?"
+++

You might have noticed me tweeting a bunch about RISC-V in recent months. It is 
actually something I have been following for several years now, since the 
formation of [LowRISC](https://www.lowrisc.org/) in Cambridge quite some time 
ago, but this year has suddenly seen a huge maturing of the ecosystem.

In case you have been sitting under a rock hacking on something for some time, 
RISC-V is an open instruction set for CPUs. It is pronounced "risk five". It 
looks a bit like MIPS, if you know your instruction sets, and yes it is very 
RISC, pretty minimal really. It is designed to be cleanly extended, and has 32, 
64 and 128 bit implementations. So far the 32 bit version is for 
microcontrollers, the 64 bit for operating systems like Linux with MMUs, and 
the 128 bit version is for future dreams.

But an instruction set, even one without licensing and patent issues, is not 
that interesting on its own. There are some other options there after all, 
although they all have some issues. What is more interesting is that there are 
open and freely modifiable open source implementations. Lots of them. There are 
proprietary ones too, and hybrid ones with some closed IP and some open, but 
the community has been building open. Not just open cores, but new open 
toolchains (largely written in Scala) for design, test, simulation and so on.

<figure>
<a href="https://scs.sifive.com/core-designer/"><img width="80%" 
src="/sifive.png"></a>
<figcaption>SiFive <a href="https://scs.sifive.com/core-designer/">core designer</a></figcaption>
</figure>

The size of the community growth this year has been huge, starting with the 
[launch by SiFive](https://archive.fosdem.org/2018/schedule/event/riscv/) of 
the first commercially available RISC-V machine that could run Linux at Fosdem 
in January. Going to a RISC-V meetup (they are springing up in [Silicon 
Valley](https://www.meetup.com/Bay-Area-RISC-V-Meetup/), 
[Cambridge](https://www.meetup.com/Cambridge-RISC-V-Meetup-Group/), 
[Bristol](https://www.meetup.com/Bristol-RISC-V-Meetup-Group/) and 
[Israel](https://www.meetup.com/Israel-RISC-V-meetups/)) you feel that this is 
hardware done by people who want to do hardware like open source software is 
done. People are building cores, running in silicon or on FPGA, tooling, secure 
enclaves, operating systems, VC funded business and revenue funded businesses. 
You meet people from Arm at these meetups, finding out what is going on, while 
Intel is funding RISC-V businesses, as if they want to make serious competition 
for Arm or something! Meanwhile MIPS has opened its ISA as a somewhat late 
reaction.

A few years ago RISC-V was replacing a few small microcontrollers and custom 
CPUs, now we see companies like Western Digital announcing they will switch all 
their cores to RISC-V, while opening their designs. There are lots of AI/TPU 
cores being built with RISC-V cores, and [Esperanto](https://www.esperanto.ai/) 
is building chips with over a thousand 64 bit RISC-V cores on. The market for 
specialist AI chips came along at the same time as RISC-V was maturing, and it 
was a logical new market.

RISC-V is by no means mature; it is forecast it will ship 10-100 million cores in 2019, 
the majority of them 32 bit microcontrollers, but that adds to the interest, it 
is at the stage where you can now start building things, and lots of people are 
building things for fun or serious reasons, or porting code, or developing 
formal ISA models or whatever. Open source wins because a huge community just 
decides it is the future and rallies around every piece of the ecosystem. 2018 
was the year that movement became really visible for RISC-V.

I haven't started hacking on any RISC-V code yet, but I have an idea for a 
little side project, but I have joined the RISC-V Foundation as an individual 
member and hope to get to the [RISC-V 
Workshop](https://tmt.knect365.com/risc-v-workshop-zurich/) in Zurich and 
several meetups. See you there and happy hacking!

