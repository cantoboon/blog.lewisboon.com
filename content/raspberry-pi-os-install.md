---
title: "Installing Ubuntu for Raspberry Pi with rpi-imager"
date: 2022-04-02T12:53:46+01:00
tags:
  - raspberry-pi
  - linux
draft: false
---

Most Raspberry Pi install guides focus just on installing the OS using
the Raspberry Pi Imager. This is a cross platform GUI that allows you
to pick an image and your SD card and it will install the OS for you.

The Imager is great for beginners and makes it difficult to go wrong.
However, I want to be able to script around the OS installation to add
additional config like [kernel command line parameters](https://www.kernel.org/doc/html/v4.14/admin-guide/kernel-parameters.html)
automatically.

In this guide I'm going to be flashing the SD card using a server running Ubuntu Server 20.04.
Other distributions should be able to adapt the commands.

## Getting the image

You can find the Ubuntu image for Raspberry Pis [here](https://ubuntu.com/download/raspberry-pi).
Make sure you use this image, and not the normal Ubuntu Server image. That is designed for x86 systems
using the GRUB boot loader.

Currently, the URL for the 20.04 Raspberry Pi image is: 
`https://cdimage.ubuntu.com/releases/20.04.4/release/ubuntu-20.04.4-preinstalled-server-arm64+raspi.img.xz`

```bash
wget https://cdimage.ubuntu.com/releases/20.04.4/release/ubuntu-20.04.4-preinstalled-server-arm64+raspi.img.xz
```

## Finding your SD card

After you plug in your SD card, you'll need to find it.

```bash
lsblk
```

Your SD card will be recognisable by its size but also its lack of mount point.

Here is an output from my server:

```bash
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
...
sdd                         8:48   1 223.6G  0 disk
├─sdd1                      8:49   1   512M  0 part /boot/efi
├─sdd2                      8:50   1     1G  0 part /boot
└─sdd3                      8:51   1 222.1G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0   100G  0 lvm  /
sde                         8:64   1  14.9G  0 disk
```

Here we can see that `sde` is my SD card. It has the same size and isn't mounted.

**Make sure you get the right device. If you run the following commands with the wrong one, you could destroy data on other drives and can potentially ruin your operating system.**

## Flashing the card

Now that you have your SD card and image, you can flash the drive. You will need to use
the name of the `disk` from earlier. This should be a 3-letter string with no numbers.

We use a command, `xzcat`, to decompress and stream the data into the [dd](https://en.wikipedia.org/wiki/Dd_(Unix)) tool.

```bash
xzcat ubuntu-20.04.4-preinstalled-server-arm64+raspi.img.xz | sudo dd of=/dev/sde status=progress bs=4M
sync
```

Note how we prefixed `/dev/` onto the disk name from earlier. Also, don't forget the `sync` command.
It ensures that cached writes are flushed to the device.

## Verifying the install

Re-running `lsblk` now shows us:

```
sde                         8:64   1  14.9G  0 disk
├─sde1                      8:65   1   256M  0 part
└─sde2                      8:66   1     3G  0 part
```

- `sde1` is the boot partition. This contains some config files that you can tweak to manipulate
the Raspberry Pi. This is where you would create the SSH file to enable SSH on Raspbian, for example.
- `sde2` is the partition containing the actual linux file system. Here you'll see all your tradition
Linux directories, like `/etc`, `/usr`, `/var`, etc.

You can mount these partitions and make changes to them.

```
sudo mkdir /mount/micro
sudo mount /dev/sde1 /media/micro
```

The directory name I'm mounting is arbitrary, but it must exist.
After you're done you can unmount it with:

```
sudo umount /media/micro
```

## Resources

- [Create installation media for Ubuntu](https://ubuntu.com/download/iot/installation-media#ubuntu)