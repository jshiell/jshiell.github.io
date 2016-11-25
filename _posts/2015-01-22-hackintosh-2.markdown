---
layout: post
title: Hackintosh II - The Workintosh
---
Late last year I started with a new client who asked that we provide our own hardware. Initially I took my 13" MBP in every day; however, it's a pain in the arse to carry on the bike and somewhat underpowered compared to a real workstation. Further, the pairing stations that were available for use were all Ubuntu machines, while for all its (increasing) faults I'd much rather stick with OS X. So I decided it was time for another Hackintosh.

My goals for this one were a little different. Rather than aiming for raw power & gaming support I wanted something that was reasonably portable and very quiet while still packing a decent punch for development work. And, of course, it needed to be compatible with OS X. Thanks to the good people at [TonyMacx86](http://www.tonymacx86.com) I ended up with the following parts.

###### Gigabyte GA-Z97N-WIFI

This is a micro-ITX board with an included micro-PCI-E board for WIFI/BT4.0 and a single PCI-E 16x slot. It's very capable, but does have a couple of drawbacks. Firstly, the built WiFi doesn't work in OS X, and while there are reports of the BT working, I've had no success. Luckily, neither were of interest to me, and the problem is easily solved by replacing the card.

More problematically, the board provides 2xHDMI 1.4a ports and a single DVI-I port. There's no dual-link DVI support, so you're limited to HDMI for 1440p monitors. However, many of the Dell monitors don't support more than 1080p on HDMI, which lead me to add a discrete graphics card rather late in the build.

**Intel i5 4790**: I'm a big fan of the i5 over the i7. I remain of the opinion that unless you're ruinning a heavy multi-core workload then the performance gain is really not worth the substantial price increase. Given this was a work computer I also skipped the K series, instead taking the 4W saving of the virgin part.

**Cooler Master Hyper 212 EVO**: It's gigantic, effective and quiet. It also fits inside the case. Just.

**Crucial Ballistix Sport 16GB (8GBx2) DDR3 PC3-12800**: The Z97N-WIFI only offers 2 slots, so 16GB seems a good way to fill them.

**Samsung 840 EVO 250GB SSD**: While now replaced by the 850, these drives are magic. The price is superb for the performance, even although you do need to be sure you've upgrading the firmware before using them.

**Corsair 550W CS550M ATX/EPS Semi-Modular PSU**: I love the modular PSU in my home box; this is a slightly smaller version, as the case is somewhat tight on the PSU length. But 550W is heaps for this build, plus it's quiet and effective. The only slight surprise was that the PCI-E power cable differs from that on my 650HX, leading to some embarassment when wiring in the video card.

**BitFenix Prodigy Mini-ITX Cube Case (Black)**: I'm very chuffed with this. While it's a bit tight around the PSU, it has heaps of drive mounts, decent cooling and can also fit a full video card. It's also plastic and feels very light next to my Corsair 500R. The downsides are not great: the PowerMac-eqsue fins are plastic and wobbly, and really not suitable for carrying the case; and the unused 5.25" bay is a touch ugly.

**EVGA GTX 750 Ti FTW 2GB**: As mentioned, this was a late addition when I discovered the Dell monitors at work wouldn't run at full resolution over HDMI. I wanted displayport, which turns out to be hard to find on the cheap cards. And this one is a little overpowered -  I got lucky in finding a refurbished card, as otherwise I'd have stuck to a low profile GTX750.

Software installation was pretty smooth - the initial build with the onboard HD4600 was painless, using Clover 3113. There was a kernel panic thanks to the ALXEthernet driver; I replaced it with the [E2200 driver](http://www.insanelymac.com/forum/topic/300056-solution-for-qualcomm-atheros-ar816x-ar817x-and-killer-e220x/) and it's been perfect since.

The video card did require the [nVidia web drivers](http://www.nvidia.com/download/driverResults.aspx/79077/en-us), but was otherwise painless. I did see some minor oddities post-sleep, but these seem to have vanished now I've disabled the onboard graphics in the UEFI.

I'm very happy with the result. It's quiet enough that I'm tempted to build one for home, and portable enough to be carried on the bus. And, best of all, I can finally stop carrying the laptop everywhere.

##### Update - WiFi & Bluetooth

As I'm a completionist, I've now ordered an *Azurewave AW-CE123H* 802.11(b|g|n|ac) + BT4 PCI-e mini card, to replace the incompatible one supplied by Gigabyte. By all accounts it's fully compatible, so this should (in theory) give AirDrop & Continuity support. [I've recorded the results here.](/2015/02/06/the-final-touch.html)

