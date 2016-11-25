---
layout: post
title: boot2docker vs the Network
---
If you're using Docker on a Mac then you're most likely doing it via [boot2docker](https://github.com/boot2docker/boot2docker), which is a lovely puff of pixie dust to hide the fact you're running Docker in a Linux VM. Even more impressively, the problems I've so far encountered can all be laid at the door of [VirtualBox](https://www.virtualbox.org)[^1] - in particular, networking. And God forfend you should be using the horror that is Cisco AnyConnect.

Luckily, there's a workaround for the networking excitment - merely add a port forward to the VM, and then access docker via 127.0.0.1.

To add the port forward, make sure your VM isn't running and run:
```
vboxmanage modifyvm "boot2docker-vm" --natpf1 "docker,tcp,127.0.0.1,2376,,2376"
```
Then, change your DOCKER_HOST environment variable to point at localhost:
```
export DOCKER_HOST=tcp://127.0.0.1:2376
```
If you're lazy (as I am), you can go one further and add the DOCKER_\* variables into your profile and never need to run *boot2docker shellinit* ever again.
```
export DOCKER_HOST=tcp://127.0.0.1:2376
export DOCKER_CERT_PATH=$HOME/.boot2docker/certs/boot2docker-vm
export DOCKER_TLS_VERIFY=1
```
Of course with the announcement of [Docker Machine](http://blog.docker.com/2015/02/announcing-docker-machine-beta/) - which supports Fusion and potentially [Parallels](https://github.com/docker/machine/pull/436) in the near future - this workaround is hopefully not long for this world. But in the meantime, it's certainly remove boot2docker as an annoyance in my workflow.

[^1]: You can use [VMWare Fusion](http://blog.gnu-designs.com/howto-run-boot2docker-in-vmware-fusion-and-esxi-with-shipyard-to-manage-your-containers/), but you do lose the hidden VM aspect of the setup.