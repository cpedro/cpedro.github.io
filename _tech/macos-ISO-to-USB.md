---
layout: default
title: "How to make bootable USB from an ISO on macsOS"
---

If you need to make a bootable USB stick from an ISO you've downloaded, it's
actually fairly straightforward on a Mac.  I've used this to make various
bootable installation stick, everything from Windows, Linux, VyOS and many
others, and never had an issue.

One thing I will mention, the below steps can wipe your system drives or other
external disks you have plugged into your computer, specifically on step 7, and
cause you to lose data or make your computer unbootable. So please make sure
you're running the commands with the right disk numbers, and always **backup
your data**.

1. Download the ISO file, and open up your favourite Terminal application.
1. Connect the USB stick you wish copy to. You don't need to pre-format this
disk at all.  In fact, formatting it will be a waste of time.
1. Convert the .iso to an .img file.  This is listed as a step on many sites,
but I can't tell if actually does anything.  Replace `~/Downloads/...` with the
path to where you downloaded the ISO to.
    ```
    $ hdiutil convert -format UDRW -o target.img ~/Downloads/file.iso
    ```
1. macOS will put `.dmg` extension on the end of the file (`target.img.dmg`).
You can strip this off.
    ```
    $ mv target.img{.dmg,}
    ```
1. Run the below command to get a list of connected disks.  Make note of which
disk is the USB stick.  This should be easy to tell by looking at the "SIZE"
column.  If not, unplug the USB stick, run the command, then plug it back in
and run it again to see what disk appeared when you plugged it in.  The USB
stick should be disk 2, 3 or maybe 4.  It will also usually say `(external,
physical)` next to the right one, but if you have any other external drive
plugged into the Mac, they will also say the same thing, so **be careful**.
You don't want to wipe the wrong drive.  As an example, here's the command run
on my machine, and `/dev/disk3` is an 8GB USB stick I have plugged in.  Disks
0, 1 & 2 are my internal Fusion drive.
    ```
    $ diskutil list
    /dev/disk0 (internal, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      GUID_partition_scheme                        *1.0 TB     disk0
       1:                        EFI EFI                     209.7 MB   disk0s1
       2:                 Apple_APFS Container disk2         1000.0 GB  disk0s2

    /dev/disk1 (internal):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      GUID_partition_scheme                         28.0 GB    disk1
       1:                        EFI EFI                     314.6 MB   disk1s1
       2:                 Apple_APFS Container disk2         27.7 GB    disk1s2

    /dev/disk2 (synthesized):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      APFS Container Scheme -                      +1.0 TB     disk2
                                     Physical Stores disk1s2, disk0s2
       1:                APFS Volume Macintosh HD            127.7 GB   disk2s1
       2:                APFS Volume Preboot                 47.3 MB    disk2s2
       3:                APFS Volume Recovery                517.0 MB   disk2s3
       4:                APFS Volume VM                      6.5 GB     disk2s4

    /dev/disk3 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:     Apple_partition_scheme                        *8.0 GB     disk3
       1:        Apple_partition_map                         4.1 KB     disk3s1
       2:                  Apple_HFS                         720.9 KB   disk3s2
    ```
1. Run the below to unmount the disk.  Replace `N` with the disk number of the
USB stick.
    ```
    $ diskutil unmountDisk /dev/diskN
    ```
1. The next command will erase the USB stick and replace its contents with the
downloaded ISO file.  Make sure you do this on the right disk, or you **risk
wiping the wrong device**.  Again, replace `N` with your disk number.  You'll be
prompted for a password, just enter the password you use to log onto your Mac.
    ```
    $ pv target.img | sudo dd of=/dev/rdiskN bs=1m
    ```
1. After this command finishes, you might get a warning pop up saying the disk
inserted was not readable by this computer.  You can click 'Ignore'.  It just
means macOS doesn't know how to read the USB stick, but it will be bootable,
don't worry.
1. Eject the disk by running the below command.  Replacing `N` again for the
disk number.  If you clicked 'Eject' instead of 'Ignore' when the warning popped
up, you can skip this step.
    ```
    $ diskutil eject /dev/diskN
    ```
1. That's it!  You can remove the USB stick and use to boot off on any other
device, or your Mac.

Adapted from [Original Source](https://help.ubuntu.com/community/How%20to%20install%20Ubuntu%20on%20MacBook%20using%20USB%20Stick)
