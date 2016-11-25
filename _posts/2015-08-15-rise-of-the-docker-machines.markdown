---
layout: post
title: The Rise of the [Docker] Machines
---
Given [boot2docker-cli](https://github.com/boot2docker/boot2docker-cli) is now officially deprecated, there's never been a better time to switch to [docker-machine](https://github.com/docker/machine). While I'm still finding the [VMWare Fusion driver somewhat flakey](https://github.com/docker/machine/issues/1671), the VirtualBox driver is solid and easy to switch to.

Start by deleting boot2docker - it'll avoid confusion, and saves pain if you've scripts that need to deal with both cases.
```
boot2docker stop && boot2docker delete; brew remove boot2docker; rm -rf ~/.boot2docker
```

Then install docker-machine, which is now available via Brew.
```
brew update && brew install docker-machine
```

And now you can set up your VirtualBox VM. I use the name *boot2docker-vm*, as it means I don't need to change scripts that already check for that VM name (e.g. when setting up mapped ports).

```
docker-machine create --driver virtualbox --virtualbox-cpu-count 2 --virtualbox-memory 2048 boot2docker-vm
eval $(docker-machine env boot2docker-vm)
```

There's one big difference I've found, and that's that the generated TLS certificate is now tied to the IP address of the VM. This means mapping the VM to localhost, as described in my [previous post](https://infernus.org/boot2docker-vs-the-network/), no longer works.

```
$ docker ps      
An error occurred trying to connect: Get https://127.0.0.1:2376/v1.20/containers/json: x509: certificate is valid for 192.168.99.100, not 127.0.0.1
```

There's [an incoming fix for this](https://github.com/docker/machine/pull/1228), allowing you to specify useful hostnames to use in the certificate (i.e. localhost), but in the meantime the easiest workaround is to turn off TLS verification via an alias. Do be aware that this will break the `docker-machine active` command, and hence isn't suitable if your scripts rely on that.

```
docker-machine stop boot2docker-vm
vboxmanage modifyvm "boot2docker-vm" --natpf1 "docker,tcp,127.0.0.1,2376,,2376"
export DOCKER_HOST="tcp://127.0.0.1:2376"
alias docker='docker --tlsverify=false'
```

Bypassing TLS is pretty unpleasant in principle, but hopefully we'll be able to restore it in the not too distant future.