---
layout: default
title: "Useful Linux Commands"
---

Below is just a random dump of useful commands I use frequently, in no 
particular order.

In case you're unfamiliar with the syntax, if you see `$` it means you can run
the command as an unprivileged user, `#` needs to be run as root.

## Run Local HTTP Server Using Python

This is usable on any machine running Python.  Useful for copying new OS images
to a Cisco or VyOS router from a laptop or something else, without running a
full blow webserver.  There's different commands based on which version of 
python you're running.

#### Python 3:
```
$ python -m http.server 8000
```

#### Python 2:
```
$ python -m SimpleHTTPServer 8000
```

## Zero out the MBR on a disk
Useful if you quickly want to re-install an OS from PXE or CD, but don't want
to muck around with changing the BIOS boot order.  Data will still be on the
disk, but it just blows away the MBR.  Change `/dev/sda` to your boot disk.
```
$ sudo dd if=/dev/zero of=/dev/sda count=1 bs=512
```

## Show Interface Error Counts
Show the RX and TX errors on an interface in a nice readable format.  Change 
`<int>` to be whatever interface you want to see stats for. Adapted from 
[here](https://serverfault.com/questions/702555/how-to-troubleshoot-rx-missed-errors).
```
# for i in /sys/class/net/<int>/statistics/*; do echo -n "${i##*/}: "; cat $i; done
collisions: 0
multicast: 0
rx_bytes: 0
rx_compressed: 0
rx_crc_errors: 0
rx_dropped: 0
rx_errors: 0
rx_fifo_errors: 0
rx_frame_errors: 0
rx_length_errors: 0
rx_missed_errors: 0
rx_over_errors: 0
rx_packets: 0
tx_aborted_errors: 0
tx_bytes: 0
tx_carrier_errors: 0
tx_compressed: 0
tx_dropped: 0
tx_errors: 0
tx_fifo_errors: 3
tx_heartbeat_errors: 0
tx_packets: 0
tx_window_errors: 0
```

## Add VLAN Interface
Check if module is loaded.  If not, load it:
```
$ modinfo 8021q
$ modprobe --first-time 8021q
```

Commands to add VLAN interface, assign IP and up.
```
$ sudo ip link add link <dev> name vlan<X> type vlan id <X>
$ sudo ip addr add <ip>/<prefix> dev vlan<X>
$ sudo ip link set vlanX up
```

## Add static route
```
$ sudo ip route add <network>/<prefix> via <gw> dev <dev>
```

## Remount partition as read/write
```
$ sudo mount -o remount,rw <partition>
```

## Remount partition as read-only
```
$ sudo mount -o remount,ro <partition>
```

## Identify Ethernet interface by PCI address
I've used this multiple times with some boxes that have many interfaces.

First run `lscpi` to make get the PCI address of all Ethernet interfaces.  The
PCI address is the first part of the message, eg. "01:00.0"
```
# lspci | grep Ethernet
02:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
02:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
02:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
02:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
04:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
04:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
04:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
04:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
06:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
06:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
06:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
06:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
08:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
08:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
08:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
08:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0a:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0a:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0a:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0a:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0c:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0c:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0c:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0c:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
0e:00.0 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE SFP+ (rev 02)
0e:00.1 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE SFP+ (rev 02)
0e:00.2 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE SFP+ (rev 02)
0e:00.3 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE SFP+ (rev 02)
11:00.0 Ethernet controller: Intel Corporation I210 Gigabit Network Connection (rev 03)
```

Next, run `dmesg` to show the interfaces and their PCI addresses:
```
# dmesg | grep 'renamed from' | egrep 'igb|i40e' | awk '{print $5, $4}' | sort
enp12s0f0: 0000:0c:00.0
enp12s0f1: 0000:0c:00.1
enp12s0f2: 0000:0c:00.2
enp12s0f3: 0000:0c:00.3
enp14s0f0: 0000:0e:00.0
enp14s0f1: 0000:0e:00.1
enp14s0f2: 0000:0e:00.2
enp14s0f3: 0000:0e:00.3
enp17s0: 0000:11:00.0
enp2s0f0: 0000:02:00.0
enp2s0f1: 0000:02:00.1
enp2s0f2: 0000:02:00.2
enp2s0f3: 0000:02:00.3
enp4s0f0: 0000:04:00.0
enp4s0f1: 0000:04:00.1
enp4s0f2: 0000:04:00.2
enp4s0f3: 0000:04:00.3
enp8s0f0: 0000:08:00.0
enp8s0f1: 0000:08:00.1
enp8s0f2: 0000:08:00.2
enp8s0f3: 0000:08:00.3
ens15f0: 0000:06:00.0
ens15f1: 0000:06:00.1
ens15f2: 0000:06:00.2
ens15f3: 0000:06:00.3
ens1f0: 0000:0a:00.0
ens1f1: 0000:0a:00.1
ens1f2: 0000:0a:00.2
ens1f3: 0000:0a:00.3
```
