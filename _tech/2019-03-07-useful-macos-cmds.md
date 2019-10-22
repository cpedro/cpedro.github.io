---
title: "Useful macOS Commands"
layout: default
---

Last Updated: 2019-10-22

Below is just a random dump of useful commands I use frequently, in no 
particular order.

## Fix zsh warning in Catalina

Since macOS Catalina, the default shell has been changed from bash to zsh. If
like me, you still prefer bash and use it as your shell, you'll get the warning
"default interactive shell is now zsh" each time you log into a terminal.  To
silence this, you can put `BASH_SILENCE_DEPRECATION_WARNING=1` in your
`.bash_profile`. [Source](https://www.addictivetips.com/mac-os/hide-default-interactive-shell-is-now-zsh-in-terminal-on-macos/)

```
$ echo 'export BASH_SILENCE_DEPRECATION_WARNING=1' >> ~/.bash_profile
```

## Flush Local DNS

```
$ sudo killall -HUP mDNSResponder
```

## Find open TCP and UDP sockets

```
$ lsof -Pnl +M -i4
```

To find just TCP in LISTEN state
```
$ lsof -Pnl +M -i4 | grep LISTEN
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

