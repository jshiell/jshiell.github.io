---
layout: post
title: nVidia Web Drivers & Hackintosh iMacs
---
When using a Hackintosh, one of the silly things you can do to hurt yourself is to use hardware without built-in support. At present, this includes the nVidia GTX 970. It's a superb card, and plays almost anything with great aplomb, but the built-in drivers in OS X 10.10.(0,1) won't have anything to do with it. As such, you'll need  the [nVidia Web Drivers](http://www.nvidia.com/download/driverResults.aspx/79077/en-us).

There's just one small catch - these only allow installation on Mac Pros (3,1; 4,1; 5,1). While making perfect sense in the world of real Macs, many of us run our Hackintoshes as pretend iMacs, as the Sandy Bridge/Ivy Bridge/Haswell builds are much closer to iMac hardware than the Xeon-based Mac Pros.

Never fear, however - you can easily alter the installation package to skip the system check.

First, download the driver packages. Then, extract them to a temporary directory:
```
pkgutil --expand WebDriver-343.01.02f01.pkg working
```
Next, edit the **working/Distribution** file, changing the *validateHardware()* method to return **true** in some manner. I changed line 63 to:
```
if (found_hardware == -1)
```
Finally, package it all up again:
```
pkgutil --flatten working webdriver.pkg
```
You now have a modified package file that can be run without changing your SMBIOS. Run, reboot, and enjoy!