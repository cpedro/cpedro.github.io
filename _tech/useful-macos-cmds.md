---
layout: default
title: "Useful macOS Commands"
---

Below is just a random dump of useful commands I use frequently, in no 
particular order.

## Flush Local DNS

```BASH
$ sudo killall -HUP mDNSResponder
```

## Add VLAN Interface

```BASH
$ sudo ifconfig vlan<X> create
$ sudo ifconfig vlan<X> vlan <X> vlandev <interface>
$ sudo ifconfig vlan<X> inet <IP> netmask <netmask>
$ sudo ifconfig vlan<X> up
```

## Add Static Route

```BASH
$ sudo route -n add -net <ip>/<prefix> <gw>
```
