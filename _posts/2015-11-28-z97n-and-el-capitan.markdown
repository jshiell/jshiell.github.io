---
layout: post
title: Z97N-WIFI / Azurewave CE-123H and El Capitan
---
[In an earlier post](https://infernus.org/2015/02/06/the-final-touch.html) I documented getting the AW CE-123H WiFi/BT4 module playing nicely with Yosemite. It turns out to be even easier with El Capitan thanks to [the hard work of RehabMan and the-darkvoid](https://github.com/the-darkvoid/BrcmPatchRAM). No Clover patches are needed anymore, merely a couple of kexts to sort out the BT firmware and the WiFI device ID.

For Bluetooth support, [download the BrcmPatchRAM package](https://bitbucket.org/RehabMan/os-x-brcmpatchram/downloads/RehabMan-BrcmPatchRAM-2015-1016.zip) and copy *BrcmBluetoothInjector.kext* to */Clover/kexts/10.11*.

For WiFi, [download the Fake PCI ID package](https://bitbucket.org/RehabMan/os-x-fake-pci-id/downloads/RehabMan-FakePCIID-2015-0703.zip) and copy *FakePCIID_BCM94352Z_as_BCM94360CS2.kext* to */Clover/kexts/10.11*.

Reboot, and victory should be yours.