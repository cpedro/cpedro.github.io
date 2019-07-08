---
title: "Useful macOS Commands"
layout: default
---

Last Updated: 2019-03-19

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

## Remove VLAN Interface

```
$ sudo ifconfig vlan<X> destroy
```

## Show Routes

General routing table:
```
$ netstat -rn
```

Specific route:
```
$ route get <net|ip>[/<prefix>]
```

## Editing Static Routes

### Adding

Network route:
```
$ sudo route add -net <net>/<prefix> <gw>
```

Host route
```
$ sudo route add -host <ip> <gw>
```

### Changing

Network route:
```
$ sudo route change -net <net>/<prefix> <new-gw>
```

Host route
```
$ sudo route change -host <ip> <new-gw>
```

### Removing

Network route:
```
$ sudo route delete -net <net>/<prefix>
```

Host route
```
$ sudo route delete -host <ip>
```

