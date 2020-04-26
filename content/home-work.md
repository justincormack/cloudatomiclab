+++
author = "Justin Cormack"
date = "2020-04-26T18:00:00Z"
description = "Home Work Setup"
tags = []
title = "Home Work Setup"
+++

The other day I got a message from Jenny "the Burce" saying that I had to get 
some equipment to upgrade my live streaming setup for the DockerCon dry run. 
Cameras and microphone and things, a list from [Bret 
Fisher](https://www.bretfisher.com/). Only problem, I soon discovered, was that 
nothing on the list was actually available. Somehow just after lockdown 
everything that people might need to live stream audio and video had been panic 
bought, along with the flour, toilet paper and eggs. So over the next month or 
so I have gradually put together a setup that works, with the aim of improving 
the audio and video quality.

It has also been the first time I have worked at home for long periods, 
previously I mostly went to the office with a few meetings at home at the start 
and end of the day. Given that we are all going to be homeworking for a long 
period, may as well make it better. Due to lack of availability all the low end 
stuff was unavailable, but will give some pointers and suggestions as to what 
is worthwhile or not, and supply chains should start to improve soon. I am 
lucky enough to have a reasonable amount of space, if you are working in a 
constrained space I would imagine choices are more limited.

Also I am lucky enough to be able to work at home, or at all in these difficult 
times. Tech workers are so lucky and safe compared to so many others.

<img src="/desk.jpeg" width="60%"/>

## Desk

1970s Danish teak desk, bought on the Holloway Road some years back. Not in 
perfect condition or anything, a desk for using. Hard to move around. Big, not 
going to fit in a small space. I don't remember the price, it wasn't a lot and 
it will last another 40 years. The lamp is a German, asymmetric one from the
1930s.

## Computer

MacBook Pro from a few years back. I have been wondering about getting a 
desktop as, well, not going anywhere. However I want something silent and that 
seems really difficult now. I do have a Linux box (carefully constructed with 
large slow fans) and a FreeBSD FreeNAS box under the table, but although they 
are fairly quiet I find them too noisy when working so I mostly keep them 
switched off. The cloud is silent of course, a great advantage. I may go down 
the silent PC building route again soon, will keep you posted.

## Monitor

Dell 27 inch 4k monitor I took from the office. I thought it was too small on 
this desk, but then realised I had it too far back. I would probably get a USB 
C monitor now, just to get more ports nearby but this is fine. I don't like 
double monitors due to the gap, I prefer a single large one.

## Internet connection

It is going to be difficult to improve quality for live conversations without a 
good internet connection. Obviously there may not be much choice where you are 
though, so changing this can be difficult. I use [Andrews and 
Arnold](https://www.aa.net.uk/) with 80/20Mbs VDSL; they are a high quality 
service with static IPs, IPv6, and they do not have oversubscription. It costs 
a bit more than other providers.

## Keyboard and trackpad

Need cleaning. Apple bluetooth ones. I also have a (noisy) Hacker's keyboard 
around. I much prefer trackpads to mice or trackballs now.

## Dock

Realising I was about to plug in more things than the computer has ports, I got 
the [Caldigit TS3 Plus](https://www.caldigit.com/ts3-plus/) as recommended by 
someone on Twitter. This provides power down one thunderbolt cable to the 
computer, while having everything else plug into it. It has displayport for the 
monitor, and wired ethernet, meaning I can avoid wifi issues. The wired 
ethernet goes via ethernet over mains adaptors downstairs to the router. Note 
that if you have the new MacBook Pro 16 inch, this consumes a peak 97W of power 
which is more than this delivers although maybe there will be a firmware fix. 
CPU peak power consumption is getting ridiculous now, 100W laptops!

## Webcam

I managed to order a [Logitech 
StreamCam](https://www.logitech.com/en-us/product/streamcam) direct from 
Logitech just before all webcams sold out. It is excellent quality, see 
pictures below. I sit it on top of the monitor, and it has USB C. It has a very 
wide angle of view, but I eventually found out that the Logitech Camera 
Settings App allows you to modify this, with a narrower setting too. This is 
just a crop, so it is not as high quality. The Logitech software is much worse 
on Mac than Windows it seems, with far less control available; some of the 
Windows controls appear to be done in software with a software video out that 
other applications can connect to which is not available on Mac. The Logitech 
4k cameras apparently have three zoom options as well as ability to set frame 
rates, and it looks like some stock may become available again, so these could 
be better for a cropped view. Actually using the 4k option is not really 
possible with most software at present though, and it requires lots of CPU to 
encode.

Having the camera above you on the monitor is way better than using the camera 
on a laptop, which is generally low down unless you raise it up a lot; also as 
you want to use a monitor generally the laptop is probably to the side, which 
looks strange on calls. I don't know why Apple do not improve the quality of 
laptop cameras to match their phone cameras, and I have heard of people using 
phones to stream.

Another option a friend is exploring is using a digital camera; most recent 
cameras can stream video although generally only via HDMI out so you need 
something like the [Elgato Cam 
Link](https://www.elgato.com/en/gaming/cam-link-4k) and these are also hard to 
get now. With a choice of lenses and zoom and excellentpicture quality this is 
an option if you already have a suitable camera; you probably want to use a 
lens around 35mm it seems. You will need to mount it behind the monitor which 
needs some work. Obviously this is a substantially more expensive option and 
only makes sense if you have a camera already for other uses.

## Lighting

Cameras are way better quality with lights. You might not immediately notice, 
so here are some crops to give you an idea of low light versus a reasonable 
light. I have the [Elgato Key 
Light](https://www.elgato.com/en/gaming/key-light), which is wifi controlled. 
You probably need something this bright, I had a small LED panel and it was not 
bright enough.

The pictures below show crops of the video in the dark without lighting, with
light from the window only and lit with additional lighting.

<img src="/dark.png" width="30%"/><img src="/shadow.png" width="30%"/><img src="/lit.png" width="30%"/>

The Key Light has a slightly annoying property of occasionally losing wifi 
access and needing to be reset, although it stays on during this time, so I am 
not sure I can entirely recommend it, although it hasn't happened for a while
now. It is also expensive, but generally good. Lights are difficult to 
buy. This clamps to the table which is good, as tripod type stands take up 
loads of desk space or floor space around.

I also have a window to the side, which provides most of the light during the 
day, but I use the light at a lower level as a fill light, or else the side of 
my face away from the window is very dark. At night I use the light as a key 
light, and don't use a fill, so it is a bit like Rembrandt lighting. Look at 
[three-point lighting](https://en.wikipedia.org/wiki/Three-point_lighting) to 
get an idea of how to place lights, you ideally want them diagonally 
notdirectly in front, or else it looks very flat. I place the webcam a little 
bit asymmetrically pointing into the room so it does not catch the very bright 
window. The worst setup is if you have a window behind you, when the camera 
will have a hard time, as you can see when having calls with people with that 
setup.

## Audio

Audio gets complicated very fast. Your options are to use your laptop, or to 
use the microphone on your webcam, which is what I was doing for a while, and 
still do sometimes. There is another problem though about how to listen to the 
audio, and avoiding the microphone picking up the sound of the other party, or 
yourself. I had a bias towards audio/music equipment as I have used it in the 
past a little and it is currently relatively easily available; there are very 
different routes you could take here.

The original recommendation from Bret was to get the [Samson 
Q2U](http://www.samsontech.com/samson/products/microphones/usb-microphones/q2u/),
but this remains totally unobtainable. Actually all USB microphones were 
unobtainable. If you get a USB dynamic microphone, such as the Q2U or the 
[Audio Technica ATR2100](https://eu.audio-technica.com/ATR2100-USB) which is 
similar but more expensive (but maybe available now) then your route will be 
simpler and cheaper than mine below.

So I went the traditional route. Generally the advice seemed to be that unless 
your room is a soundproofed studio, get a dynamic microphone not a condenser 
microphone, as they are more directional and likely to mostly pick up your 
voice not what is going on outside or downstairs or even the noise from your 
keyboard. I went for the classic [Shure 
SM57](https://www.shure.com/en-US/products/microphones/sm57) a microphone that 
has been around so long it [has its own Wikipedia 
page](https://en.wikipedia.org/wiki/Shure_SM57) and [White House 
stories](https://www.theverge.com/2017/1/25/14384774/trump-microphone-speech-lon
g-neck-shure-sm57). I ordered direct from the manufacturer which was very 
quick; apparently there are a lot of fakes of these so it is worth buying from 
a reputable place. You can't see it clearly in the photo above as it is pointing
straight at me, as I am sitting it does not obstruct the view, but I can move
it away andback on the mic stand, see below.

As the mic has XLR analogue outputs you need to plug it into the computer. The 
easiest way is to get an audio interface, that combines a microphone pre-amp 
and an analogue to digital converter. I got the [Audient 
EVO4](https://evo.audio/products/evo-4/overview/), which seems really nice and 
excellent quality. Audient is a UK company that makes mixers and other 
professional audio recording hardware; this is their "diffusion line" but has 
the same high quality hardware. This also acts as a headphone amp, and can live 
mix the audio from the mic into the headphone so you can listen to yourself 
speaking. It supports two mics, or a mic and an instrument, and there is also a 
four channel version, for a future world without social distancing when we are 
in the same room again. There is only one potential issue with this 
combination, which is that the microphone outputs at a very low level. The EVO4 
has 58dB gain, which is quite a bit more than most units I looked at, but if 
you have a quiet normal speaking voice and don't project it, even if you have 
the gain set to maximum, if you speak more than around two inches away from the 
mic it is a little quieter than ideal. At around two inches away it is fine 
although with some extra bass emphasis, or if you speak up a bit, but I am not 
really used to doing either of those most of the time on calls. I should 
probably get used to it; the 
[recommendation](https://pubs.shure.com/guide/SM57/en-US) is to be less than 
15cm away.

I ended up, in the spirit of testing every option, getting a 
[FetHead](https://www.tritonaudio.com/fethead) which is a tiny microphone 
preamp that fits inline with the mic and provides an additional 27dB of gain, 
powered from the preamp. This is designed for exactly this use case with 
dynamic microphones. Adding it suddenly shifted from having to use max gain at 
all times to being in the middle of the scale and having plenty of room to 
adjust. It also cut the small low noise level even lower. I would say if your 
preamp has less than 58dB of gain you would need this with this mic, otherwise 
you could get away without it but it gives a little more flexibility. I chose 
the EVO4 partly due to the fact it has relatively high gain, so you would get 
more choice with the FetHead as any audio interface will be fine, although the 
Evo4 is still a nice choice I think. 

Usually you are recommended to use headphones for audio recording, so as not to 
record the output sounds along with input. Much software has echo cancellation 
built in, and the Mac has some hardware cancellation, although that may just be 
on the built in microphone and speakers. This means that you don't necessarily 
need to wear headphones for many use cases, although they will give you a 
better idea of relative volume levels if you have multiple sources, and 
depending on your exact setup and mic they will reduce echo or noise. Your 
voice will sound a little different in the headphones than you are used to, but 
there is no lag, and you get used to it. Having the audio in your headphones 
stops you shouting which people tend to do with headphones as they cannot hear 
themselves and compensate. A dynamic mic like the Shure is also fine for 
recording with speakers even without cancellation, that is a normal stage 
recording setup that they are often used for, ideally with the speakers at 65 
degrees behind the mic as that is the zone of least sensitivity. I may well set 
up some speakers later; the EVO4 has line out for speakers too. It is less 
clear where to put the speakers on the desk though.

You really want a mic "boom stand" with this setup so you can move the mic out 
of the way, and then place it back in the right place, as mic placement is 
important. I had no idea about stands and got the [Neewer 
NB-35](https://neewer.com/products/microphones-accessories-90087662) which is 
very cheap, and it does the job but it is a bit annoying as the part that holds 
the mic is hard to keep at the right angle, and the whole thing moves in a 
slightly annoying way. I may try a different one.

I originally got the [Audio Technica 
M30x](https://www.audio-technica.com/cms/headphones/f6e3988012a67cd1/index.html)
 headphones. These are not too expensive, and good quality closed ear 
headphones, which block out external noise well. I did find that wearing them 
for long periods made my ears hot and slightly squashed and they are not great 
after an hour or so. I ended up getting open backed, around the ear headphones, 
[Sennheiser 
HD600](https://en-us.sennheiser.com/best-audio-headphones-high-end-stereo-hifi-h
d-600) which are way more comfortable to wear for long periods, and sound 
great. As they aren't closed, other people could hear you so you wouldn't wear 
them travelling or in a shared office, but if you have your own room to work in 
this design works really well, if you don't want total sound isolation and 
noise cancellation (you can hear the doorbell ring, which is useful). You also 
can hear yourself speak, although I do like a little microphone mixed in; you 
could use these with any kind of microphone without a mixer, and some come with 
built in mics.I tested recording while having music playing in the headphones, 
and with the Shure mic the recording level even with quite loud music is 
negligible with your head in the normal direction; if you point your ears at 
the mic it clearly picks up the sound. With a less directional mic such as the 
one in the webcam it picks up a quite a bit of the noise though. 

Overall I would say that with a dynamic microphone you get a lot more 
flexibility in your headphone options. For recording something offline I would 
probably use the closed ear headphones or not listen at all during the 
recording (the EVO4 can show mic line level). For talking to other people and 
daily use the open back headphones are so much more comfortable that they make 
a lot of sense, and you can just switch from listening to music to making calls.

I didn't make any effort to choose portable equipment, as this is lockdown, but 
other than the mic stand it is all relatively portable equipment. The EVO4 can 
be plugged into an iPod with USB C, or an iPhone if you have the [Lightning to 
USB3 Camera 
Adapter](https://www.apple.com/uk/shop/product/MK0W2ZM/A/lightning-to-usb-3-camera-adapter?fnode=97&fs=f%3Dadapter-apple-cable%26fh%3D458e%252B45c4%252B3214%252B45b0)
which despite its name is a generic USB3 adapter that accepts input 
power over another lightning port to power external devices that need 
additional power that the phone won't provide. I tested recording and playback 
on my phone with this adapter and it worked fine.

The best place I have found for buying audio equipment, other than ordering 
direct from the manufacturer, is 
[Thomann](https://www.thomann.de/gb/index.html). They are a German family firm 
but with a global online shop, and deliver fast and efficiently to the UK, and 
their prices are a lot lower than Amazon.

## Comparing the options

Below is a video of using internal camera and webcam, and internal mic, webcam 
mic, airpods and the Shure mic. I used the Zoom cloud recording, so this gives 
an idea of what someone would see and hear at the other end of a call with me, 
rather than the best quality for local recording. Note that I had the window 
open and a motorbike goes past a couple of times, but sadly not while I was 
using each microphone, but I did type on the keyboard so you can hear what some 
non directional noise pickup is like. Overall the audio quality and resistance 
to noise pickup for the Shure SM57 is substantially better than any of the 
other options. So be nice to your co-workers and improve your audio.

<video width="640" height="480" controls poster="/desk.jpeg">
  <source src="/GMT20200426-154851_Justin-Cor_640x360.mp4" type="video/mp4">
Your browser does not support the video tag.
</video> 

## Linux

I haven't yet tested any of this equipment on Linux. I use my Linux machines as 
servers not desktop machines at present. The EVO4 audio is a standard USB audio 
device so should just work, and I think the Logitech cameras in base settings 
are, but there may well be no control of settings, probably including crop, as
this is maybe not standard, I am not entirely sure. Probably best to check.

## Is it worth it?

Well, it is not necessary. As I spend a lot of time on calls and do quite a few 
conference talks that will all be online for at least the next year or so, I think 
improving the quality is worth it. The differences are noticeable as you can see
from the recordings. Audio quality makes a lot of difference to meetings, and I
would make that a priority if you want to work on something. Supply chains should
get better over the next few months so it should get easier to find more choices.
