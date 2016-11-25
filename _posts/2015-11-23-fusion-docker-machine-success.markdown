---
layout: post
title: Fusion & Docker Machine - Success
---
I've long (i.e. always) had problems with Docker Machine and VMWare Fusion - the machine creation would hang, ever awaiting a live network, and I'd end up throwing my toys and returning to VirtualBox. 

Luckily, it appears I was [far from alone](https://github.com/docker/machine/issues/1671) and [Mark Bainter](https://github.com/mbainter) came up with a set of actions that have cured the problem for me on two machines now:

```
docker-machine delete dev # or as appropriate
brew cask uninstall vmware-fusion # or trash it from /Applications
sudo rm -rf \
  /opt/homebrew-cask/Caskroom/vmware-fusion \
  /opt/boxen/homebrew/bin/vm* ~/.docker \
  /var/root/.docker
```

At this point reboot (or not if you're feeling brave), download Fusion from VMWare's site, install normally and kick off a new machine:
```
docker-machine -D create --driver vmwarefusion --vmwarefusion-memory-size 4096 --vmwarefusion-cpu-count 1 dev
```

And hopefully celebrate success!