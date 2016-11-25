---
layout: post
title: Hackintosh #3: Skyfall^H^H^H^Hlake
---
Despite CPU progress becoming a slow and steady thing, I've been looking for an excuse to rebuild my original hackintosh, [RecentConvert](http://www.tonymacx86.com/user-builds/60191-mostly-harmless-build-core-i5-3570k-ga-z77-ds3h-8gb.html#post386712). Skylake provided that excuse, at least once the good [Pike R. Alpha](https://pikeralpha.wordpress.com/2015/11/06/appleapic-kext-binary-patch-for-skylake/) found a patch to get SATA and USB working.

It turned out to be a relatively simple build - indeed, it took me longer to get Windows 10 installed on the same box, given the installer's insistence on putting the boot loader in the wrong place and in overwriting nVidia's drivers.

This isn't a full guide - merely tweaks to [the standard process](http://www.tonymacx86.com/yosemite-desktop-guides/144426-how-install-os-x-yosemite-using-clover.html) for this hardware collection. Tony also has a [generic guide to Skylake setup](http://www.tonymacx86.com/el-capitan-desktop-guides/179221-skylake-starter-guide.html) which is well worth reading7.

## The Kit

* [Fractal Design Define R5](http://www.fractal-design.com/home/product/cases/define-series/define-r5-black)
 * Along with having a nice aesthetic, it's great to work in and is lined with sound absorbing material. 
* Intel Core i5 6600K
* [Noctua NH-D15](http://noctua.at/en/products/cpu-cooler-retail/nh-d15.html)
 * Words cannot express how large this is - but it runs very quietly.
* [Gigabyte GA-Z170X-Gaming 5](http://www.gigabyte.com/products/product-page.aspx?pid=5616#ov)
 * It's surprisingly hard to find a motherboard without PCI slots, although it still has a COM port header. More importantly, it has USB 3.1.
* [Corsair Vengeance LPX 2x8Gb DDR4 3000Mhz](http://www.corsair.com/en-gb/memory/vengeance-lpx-series)
 * Buying low profile memory is a must with the NH-D15. Do note that XMP is note playing nicely with the motherboard at present - you'll either need to live without or overclock manually (or wait for a BIOS update).
* [Samsung 850EVO](http://www.samsung.com/global/business/semiconductor/minisite/SSD/global/html/ssd850evo/overview.html)
 * While it's not the fastest, it's a [relatively] cheap way to tide myself over until M.2 NVMe drives are a little more reasonably priced.

I also carried a few pieces over from my last build (and another 3 SSDs of various sizes):

* [4GB MSI GTX 970 GAMING 4G](http://uk.msi.com/product/graphics-card/GTX-970-GAMING-4G.html#hero-overview)
* [Corsair HX650 650W PSU](http://www.corsair.com/en/hx-series-hx650-power-supply-650-watt-80-plus-gold-certified-modular-psu)
* [Apple Broadcom BCM94360CD WiFi + BT](http://www.osxwifi.com/apple-broadcom-bcm94360cd-802-11-a-b-g-n-ac-bluetooth-4-0-with-adapter-for-pc-hackintosh)
 * This was a bit pricey to acquire (especially compared to something like the [Azurewave CE123-H](https://wikidevi.com/wiki/AzureWave_AW-CE123H) I run in my other box) but as a Apple card it's painless to use.

## The BIOS

Starting with optimised defaults, there's a few small tweaks:

* Fast boot: enabled
 * SATA support: all SATA
 * VGA driver: EFI driver
 * USB support: partial initial
 * PS2 support & Network support: disabled
* Windows 8/10 Features: Windows 8/10
 * CSM: disabled
 * Network: disabled
* Super I/O (COM port): disabled
* XHCI handoff: enabled
* VT-D: disabled

## Clover Configuration

There are just a few tweaks from the default required.

To get USB playing nicely with El Capitan we need to remap the device names under the *AHCI* section:

```
<key>Patches</key>
<array>
    <dict>
        <key>Comment</key>
        <string>change EHC2 to EH02</string>
        <key>Find</key>
        <data>
        RUhDMg==
        </data>
        <key>Replace</key>
        <data>
        RUgwMg==
        </data>
    </dict>
    <dict>
        <key>Comment</key>
        <string>Rename HDAS to HDEF</string>
        <key>Find</key>
        <data>
        SERBUw==
        </data>
        <key>Replace</key>
        <data>
        SERFRg==
        </data>
    </dict>
    <dict>
        <key>Comment</key>
        <string>change EHC1 to EH01</string>
        <key>Find</key>
        <data>
        RUhDMQ==
        </data>
        <key>Replace</key>
        <data>
        RUgwMQ==
        </data>
    </dict>
</array>
```

We'll also want to fix ownership, under the *Devices* section.

```
<key>USB</key>
<dict>
    <key>FixOwnership</key>
<true/>
</dict> 
```

_**Update:** As of OS X 10.11.4, you no longer need Pike's Skylake fix below._

Most importantly, we need to add [Pike's Skylake fix](https://pikeralpha.wordpress.com/2015/11/06/appleapic-kext-binary-patch-for-skylake/) to get SATA and USB working. This sits under *KernelAndKextPatches* / *KextsToPatch*:

```
<dict>
    <key>Comment</key>
    <string>Skylake AppleAPIC Patch (c) Pike R. Alpha</string>
    <key>Find</key>
    <data>
    wegQD7bw
    </data>
    <key>Name</key>
    <string>AppleAPIC</string>
    <key>Replace</key>
    <data>
    vhcAAACQ
    </data>
</dict>
```
_**Update:** The memory detection appears to be fixed as of Clover 3504, so the below should no longer be needed._

On boot OS X failed to detect my memory correctly, seeing it as two 4Gb modules. This is easily fixed with DIMM definitions under the *SMBIOS* section:

```
<key>Memory</key>
<dict>
    <key>Channels</key>
    <integer>2</integer>
    <key>Modules</key>
    <array>
        <dict>
            <key>Frequency</key>
            <string>3000</string>
            <key>Part</key>
            <string>Unknown</string>
            <key>Serial</key>
            <string>0000020B000C020B</string>
            <key>Size</key>
            <string>8192</string>
            <key>Slot</key>
            <string>0</string>
            <key>Type</key>
            <string>DDR4</string>
            <key>Vendor</key>
            <string>Corsair</string>
        </dict>
        <dict>
            <key>Frequency</key>
            <string>3000</string>
            <key>Part</key>
            <string>Unknown</string>
            <key>Serial</key>
            <string>0000020B000C020B</string>
            <key>Size</key>
            <string>8192</string>
            <key>Slot</key>
            <string>2</string>
            <key>Type</key>
            <string>DDR4</string>
            <key>Vendor</key>
            <string>Corsair</string>
        </dict>
    </array>
    <key>SlotCount</key>
    <integer>4</integer>
</dict>
```

Finally - at least after the initial install - we'll want a sane SMBIOS. You can use [CloverConfigurator](http://mackie100projects.altervista.org) to help with this. I went for a iMac17,1 product with a BIOS version of *IM171.88Z.0105.B00.1509221819* and board ID *Mac-DB15BD556843C820*.

## Device Drivers

Avoid the *CsmVideo* UEFI driver - as long as the BIOS is in Windows 8 mode you won't need it.

In *clovers/kexts/10.11* (along with FakeSMC):

* FakeSMC (of course)
* The [Atheros E2200](http://www.insanelymac.com/forum/topic/300056-solution-for-qualcomm-atheros-ar816x-ar817x-and-killer-e220x/) driver will get one network port working.
* I don't use the onboard audio, so I can't comment there (I'm running a [Steelseries Wireless H](http://www.amazon.co.uk/SteelSeries-Wireless-Headset-PC-CD/dp/B00FAN4LTI) in USB mode).
* For Intel NIC support you can try [2.1.0d0 or above of the Mausi driver](https://github.com/Mieze/IntelMausiEthernet).

## Post Installation

The nVidia web drivers have a nasty habit of going to sleep immediately after boot with the iMac 17,1 product. So, boot up with *nv_disable=1* and apply fix #4 from [the nVidia drivers problems fixes](http://www.tonymacx86.com/graphics/161256-solving-nvidia-driver-install-problems.html):

```
sudo vim /System/Library/Extensions/AppleGraphicsControl.kext/Contents/PlugIns/AppleGraphicsDevicePolicy.kext/Contents/Info.plist
# Find <key>Mac-DB15BD556843C820</key> and replace the value below with <string>none</string>
sudo kextcache -system-prelinked-kernel
sudo kextcache -system-caches
```

## Problems

So far I've run into only one issue - it quite often kernel panics on shutdown. This seems to be a problem with the nVidia web drivers, and at its worst results in a restart rather than a shutdown.

Otherwise, it appears rock solid so far, with no panics to date and perfect iCloud/iMessage functionality.