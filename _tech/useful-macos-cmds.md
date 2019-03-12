---
layout: default
title: "Useful macOS Commands"
---

Below is just a random dump of useful commands I use frequently, in no 
particular order.

## Flush Local DNS

```
$ sudo killall -HUP mDNSResponder
```

## Add VLAN Interface

```
$ sudo ifconfig vlan<X> create
$ sudo ifconfig vlan<X> vlan <X> vlandev <interface>
$ sudo ifconfig vlan<X> inet <IP> netmask <netmask>
$ sudo ifconfig vlan<X> up
```

## Add Static Route

```
$ sudo route -n add -net <ip>/<prefix> <gw>
```
