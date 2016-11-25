---
layout: post
title: Docker Machine
---
Last week Docker announced a trio of new toys - [Machine, Swarm and Compose](http://blog.docker.com/2014/12/announcing-docker-machine-swarm-and-compose-for-orchestrating-distributed-apps/). Machine appears to be a replacement for boot2docker, as well as furthering the concept to allow your Docker host to be based not just in a VM, but also remotely.

At present, it's at version 0.1.0 so probably only of interest to the brave. Installation reflects this - [download the binary](https://docs.docker.com/machine/), mark it as executable and add it to the path:
```
$ curl https://github.com/docker/machine/releases/download/v0.1.0/docker-machine_darwin-amd64 > /usr/local/bin/docker-machine
$ chmod +x /usr/local/bin/docker-machine
```
You can then create a virtual machine via the client, in this case with a local installation of [VMWare Fusion 7](http://www.vmware.com/uk/products/fusion) and 2Gb of RAM (it defaults to 1Gb):
```
$ docker-machine create --driver vmwarefusion --vmwarefusion-memory-size 2048 dev
INFO[0000] Creating CA: /Users/jshiell/.docker/machine/certs/ca.pem
INFO[0000] Creating client certificate: /Users/jshiell/.docker/machine/certs/cert.pem
INFO[0001] Downloading boot2docker.iso to /Users/jshiell/.docker/machine/cache/boot2docker-vmw.iso...
INFO[0037] Creating SSH key...
INFO[0037] Creating VM...
INFO[0038] Waiting for VM to come online...
INFO[0064] "dev" has been created and is now the active machine.
INFO[0064] To point your Docker client at it, run this in your shell: $(docker-machine env dev)
```
We can now see our VM via *ls*:
```
$ $(docker-machine env dev)
$ docker-machine ls
NAME   ACTIVE   DRIVER         STATE     URL                          SWARM
dev    *        vmwarefusion   Running   tcp://192.168.151.128:2376
```
However, docker doesn't pick this up by default:
```
$ docker run busybox echo hello world 
FATA[0000] Cannot connect to the Docker daemon. Is 'docker -d' running on this host?
```
Instead we must either pass the docker-machine config into docker commands:
```
$ docker $(docker-machine config dev) run busybox echo hello world
Unable to find image 'busybox:latest' locally
511136ea3c5a: Pull complete
df7546f9f060: Pull complete
ea13149945cb: Pull complete
4986bf8c1536: Pull complete
busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.

Status: Downloaded newer image for busybox:latest
hello world
```
Or, we can export the configuration as you would with boot2docker:
```
$ $(docker-machine env dev)
$ docker run busybox echo hello world
hello world
```
Finally, we can start/stop the VM in a similar manner to boot2docker:
```
$ docker-machine stop dev
```
This is a lot more pleasant than boot2docker if you're intending to use a backend other than VirtualBox. And while Docker are very much emphasising the early nature of this it seems solid enough for use on development environments. I'll certainly be swapping over at work, just for the ability to use Fusion transparently.

**An update 10/3:** the Fusion driver is [currently not mapping volumes correctly](https://github.com/docker/machine/issues/641), so tread carefully.

**An updated 13/6:** there's now a [dev build of boot2docker](https://infernus.org/docker-machine-vmware/) with a fix for this in place.