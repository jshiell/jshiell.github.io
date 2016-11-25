---
layout: post
title: TunnelBroker IPv6 with the Asus RT-N66U
---
My latest toy is a Asus RT-N66U Dark Knight router. While it’s ever so nice to have a router that’s configurable via telnet and obviously Linux based, IPv6 is a little harder than on an Airport. This is made messier by their having changed the interface at some point, so it’s all a bit of a kerfuffle.

So, without further ado, here’s a quick guide on configuring the RT-N66U with a Hurricane Internet TunnelBroker IPv6 tunnel on the latest firmware (3.0.0.4.270).

On the IPv6 tab:

* **Connection Type:** Tunnel 6rd
* **DHCP Option:** Disable
* **IPv6 Prefix:** The address part of the Routed /48 address of your tunnel, e.g. 2001:123:abcd::
* **IPv6 Prefix Length:** 48
* **IPv4 Border Router:** The Server IPv4 Address address of your tunnel.
* **IPv4 Router Mask Length:** 24
* **Tunnel MTU:** The default, unless you’ve changed it on the Advanced tab for your tunnel. e.g. on BT Infinity this should be 1472 on both ends.
* **Tunnel TTL:** 255
* **IPv6 DNS Server 1:** Your choice, I favour Google: 2001:4860:4860::8888
* **Enable Router Advertisement:** Enable

If you’re on a dynamic WAN IP address (thanks BT) you’ll also need to set up DynDNS.

On the WAN -> DDNS tab:

* **Server:** TUNNELBROKER.NET
* **Host Name:** The ID of your tunnel, e.g. 12345.
* **User Name or E-mail Address:** Your hex user ID (not your username), from the Main Page on TunnelBroker.
* **Password:** Your TunnelBroker password.

Alternately, you can do it manually whenever it changes via:
```
curl -4 "https://<username>:<password>@ipv4.tunnelbroker.net/nic/update?hostname=<tunnelid>"
```

**Do please note:** the router leaves IPv6 access open, so you’ll want to either configure rules on ip6tables on the router, or to ensure everything on your LAN is firewalled. Caveat emptor.