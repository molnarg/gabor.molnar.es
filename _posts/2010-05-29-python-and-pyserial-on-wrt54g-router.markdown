---
layout: post
title: "Python and pyserial on a WRT54G router"
date: 2010-05-29 22:38
comments: true
categories: 
---

<!-- more -->

I've been trying to install python with serial support on a WRT54G router running openwrt for a day, and now it's done :)

The problem was that the router has only 4Mb of flash memory, and the whole operating system must fit into this space. The openwrt distribution itself is about 3Mb, without python installed. The python package is 2Mb, and it occupies 4Mb or more when installed.

The first solution is that you expand the storage capacity of your router by attaching an SD-card to it. You can do this easily, [this guide](http://www.yourwarrantyisvoid.com/2009/09/09/add-an-sd-card-slot-to-a-wrt54g-v2/) will help, and there is a lot of other tutorials out there.

But if you want to do it without soldering, or buying anything, there is an another solution: I've made a custom openwrt image. I wanted the router to connect to a  wireless network, but I didn't need the access point functionality, so I've cut it out, and used python-mini instead of python. The created image file was only 3.2Mb.

First of all, you have to use linux to do this. If you don't have one installed, you could use an Ubuntu LiveCD, or an other Linux LiveCD.

The first step is to download the openwrt ImageBuilder. I suggest using the newest openwrt release, which is at the time of writing Backfire, for which the ImageBuilder can be downloaded [from here](http://wiki.openwrt.org/doc/howto/obtain.firmware.generate).

After unpacking it, I used the following command to build the images:

```bash
$ make image PROFILE="Broadcom-b43" PACKAGES="-dnsmasq -e2fsprogs -kmod-fs-ext3 -kmod-ppp -kmod-pppoe -kmod-scsi-core -kmod-usb-core -kmod-usb-ohci -kmod-usb-storage -kmod-usb2 -ppp -ppp-mod-pppoe -iptables -iptables-mod-conntrack -iptables-mod-nat -kmod-ipt-conntrack -kmod-ipt-core -kmod-ipt-nat -kmod-ipt-nathelper -firewall -mtd -nvram python-mini pyserial"
```

As you can see, a lot of things were cut out, like ext3, usb, scsi, ppp, and iptables support, but python-mini and pyserial were included.

After installation, python runs fine, but if you want to import the serial module, it won't load, because the termios module is missing from the python-mini. So what now? The first thing I found that the [problem was reported](https://dev.openwrt.org/ticket/6897) to the developers of openwrt. After a while, I found a blog post which describes a solution for the problem. The author of the post built the openwrt python package from source, and copied the termios.so file to the device.

I didnt want to compile it, because it seemed difficult (at least I didnt want to read another 5 howto about building openwrt packages from source). So here is what I did:

```bash
$ cp packages/python_2.6.4-3_brcm47xx.ipk /tmp/
$ cd /tmp
$ mv  python_2.6.4-3_brcm47xx.ipk python.tar.gz
$ tar -xvzf python.tar.gz
$ tar -xvzf data.tar.gz
$ cd ./usr/lib/python2.6/lib-dynload/
$ scp termios.so root@192.168.1.1:/usr/lib/python2.6/
```

As you can see, the ipk package is just a tar.gz, and you can extract the termios.so easily. After extracting, put int in the /usr/lib/python2.6 directory on the router.

I hope this post saves you from using google more than you would want :)

