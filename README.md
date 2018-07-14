# Raspberry Pi 3 image spec (fork)

This repository contains the files with which the image referenced at
https://wiki.debian.org/RaspberryPi3 has been built.

This fork adds compatibility with the Raspi 3B+.

## Option 1: Downloading an image

See https://github.com/chschlue/raspi3-image-spec/releases to obtain the latest pre-built image.

## Option 2: Building your own image

If you prefer, you can build a Debian buster Raspberry Pi 3 image yourself. For
this, first install the
[requirements](http://git.liw.fi/vmdb2/tree/README)
of vmdb2. Then run the following:

```shell
git clone --recursive https://github.com/chschlue/raspi3-image-spec
cd raspi3-image-spec
git checkout experimental
```

Then, you can generate the image by issuing the following:

```shell
umask 022
sudo env -i LC_CTYPE=C.UTF-8 PATH="/usr/sbin:/sbin:$PATH" \
    ./vmdb2/vmdb2 --output raspi3.img raspi3.yaml --log raspi3.log
```

## Installing the image onto the Raspberry Pi 3

Plug an SD card which you would like to entirely overwrite into your SD card reader.

Assuming your SD card reader provides the device `/dev/sdb`, copy the image onto the SD card:

```shell
sudo dd if=raspi3.img of=/dev/sdb bs=64k oflag=dsync status=progress
```

Then, plug the SD card into the Raspberry Pi 3 and power it up.

The image uses the hostname `rpi3`, so assuming your local network correctly resolves hostnames communicated via DHCP, you can log into your Raspberry Pi 3 once it booted:

```shell
ssh root@rpi3
# Enter password “raspberry”
```

Note that the default firewall rules only allow SSH access from the local
network. If you wish to enable SSH access globally, first change your root
password using `passwd`. Next, issue the following commands as root to remove
the corresponding firewall rules:

```shell
nft flush ruleset
```

This will allow SSH connections globally until the next reboot. To make this
persistent, remove the line "tcp dport ssh reject with tcp reset" in `/etc/nftables.conf`.

