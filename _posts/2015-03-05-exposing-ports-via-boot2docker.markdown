---
layout: post
title: Exposing ports via boot2docker
---
One piece often missed when exposing ports via Docker on a Mac is that the VM also needs to forward the port. Otherwise, you end up with the port nicely forwarded from the Docker image to the VM, and completely hidden from your dev environment on your Mac.

This is very easily fixed, and just as easily scripted. As VirtualBox comes with a convenient command line interface, you can just bring your VM down and then add the NAT rule:
```
boot2docker down
vboxmanage modifyvm "boot2docker-vm" --natpf1 "nginx,tcp,127.0.0.1,80,,80"  
boot2docker up
```
The charmingly usable argument to vboxmanage is of the form:
```
vboxmanage modifyvm <virtual-machine> --natpf<N> "<rule-name>,<tcp|udp>,<host-ip>,<host-port>,<guest-ip>,<guest-port>"  
```
For a default boot2docker setup, you can just stick with a template:
```
PORT=8080 vboxmanage modifyvm "boot2docker-vm" --natpf1 "port-forward-$PORT,tcp,127.0.0.1,$PORT,,$PORT"  
```
You can go one further and write a simple function to check for VirtualBox and the boot2docker VM, and creating NAT forwarding rules if required. You can then include this in scripts, safe in the knowledge it'll do absolutely nothing on Linux boxes:
```
 if which VBoxManage >/dev/null 2>&1 && VBoxManage list vms 2>&1 | grep boot2docker-vm > /dev/null; then
        for PORT in 8080 8081 8082; do
            if ! VBoxManage showvminfo boot2docker-vm 2>&1 | grep "host port = $PORT" > /dev/null; then
                if ! VBoxManage controlvm boot2docker-vm natpf1 "port-forward-$PORT,tcp,127.0.0.1,$PORT,,$PORT" >/dev/null 2>&1; then
                    echo "Failed to map VirtualBox port $PORT; exiting..."
                    exit 1
                fi
            fi
        done
    fi
```