---
layout: post
title: The Final Touch
---
**Update**: [This is much easier with as of late 2015](https://infernus.org/2015/11/28/z97n-and-el-capitan.html).

The one thing missing from my work Hackintosh was WiFi & Bluetooth. While the GA-Z97N-WIFI includes an Intel mini PCI-E WiFi & Bluetooth card, it doesn't play nicely with OS X. However, rumour had it that the Azurewave AW-CE123H would do the trick. A quick trip to eBay and £26 later and one was on the way to me.

Fitting it turned out to be exciting - the existing card is screwed into a heatsink - and I'd strongly suggest sorting this out before putting the box together. Once installed it needs a few tweaks to convince OS X that it's an Airport Extreme card.

Luckily, a lot of people have worked hard to make this easy, especially the good people at [toleda](https://github.com/toleda/wireless_half-mini), with some help from [InsanelyMac](http://www.insanelymac.com/forum/topic/302410-patch-enabling-handoff-for-non-apple-bt4-devices/).

Firstly, add the [enabler kext](https://github.com/toleda/wireless_half-mini/blob/master/airport_kext_enabler/bcm4352.kext.zip) to your */EFI/CLOVER/kexts/10.10* directory. Then, edit your *clover.config* file and add the following snippets to the *KextsToPatch* array:
```
<dict>
  <key>Comment</key>
  <string>10.10-BCM94352-5 GHz-XT-ROW</string>
  <key>Find</key>
  <data>WFQAKxgsIQAg</data>
  <key>Name</key>
  <string>AirPortBrcm4360</string>
  <key>Replace</key>
  <data>WFQACjYcRwAK</data>
</dict>
<dict>
  <key>Comment</key>
  <string>10.10-BCM94352-Airport-Extreme</string>
  <key>Find</key>
  <data>axAAAHUN</data>
  <key>Name</key>
  <string>AirPortBrcm4360</string>
  <key>Replace</key>
  <data>axAAAJCQ</data>
</dict>
<dict>
  <key>Comment</key>
  <string>10.10.2-BT4LE-Handoff-Hotspot</string>
  <key>Find</key>
  <data>SIXAdFwPt0g=</data>
  <key>Name</key>
  <string>IOBluetoothFamily</string>
  <key>Replace</key>
  <data>Qb4PAAAA61k=</data>
</dict>
```

Save, reboot, and have a look at your System Report. If all has gone well, you should now have WiFi & Bluetooth with AirDrop and Handoff support.

With this done, my work Hackintosh is fully complete. It's quiet, packs a very decent punch and doesn't look at all bad.