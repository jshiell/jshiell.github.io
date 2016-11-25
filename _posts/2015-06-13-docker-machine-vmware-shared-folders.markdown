---
layout: post
title: Docker Machine & VMWare: Now with shared folders!
---
There are a few bugs in docker-machine regarding shared folders with VMWare Fusion, which is problematic if you're trying to use it to run your development environment. However, they've been hard at work sorting this out, and you can now get a VMWare Fusion box running via a dev build of boot2docker from [Fabio Rapposelli](https://github.com/frapposelli).

    docker-machine create -d vmwarefusion --vmwarefusion-boot2docker-url https://github.com/frapposelli/boot2docker/releases/download/lk4/boot2docker-lk4-vmhgfs-fix.iso --vmwarefusion-memory-size 2048 boot2docker-vm

Last I heard this should all be released in boot2docker 1.7.0. Unless, of course, we've all switched to [xhyve](http://www.pagetable.com/?p=831) by then ;-)