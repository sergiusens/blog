---
Categories: [“ubuntu”]
Description: "Snappy for devices"
Keywords: [“ubuntu”, “snappy”, “iot”, “things”]
Tags: [“ubuntu”, “snappy”]
date: 2015-01-20T12:07:57-03:00
title: Snappy Things
---

A while back, Snappy was [introduced](https://developer.ubuntu.com/en/snappy/)
and it was great, while that was happening we were already working on the next
great thing, Snappy for devices, or as everyone calls it, *things*.

Today that was finally [announced](http://www.ubuntu.com/things). It’s been
lots of fun working on this. Enablement aside, we also created a very minimal
**webdm**, it is a *Web Device Management* snap framework provided in the store
which can be easily installed on existing devices by calling

    sudo snappy install webdm

On networks where it is allowed, it can be accessed by going to
http://webdm.local:4200. Here’s a quick demo of it running on a BeagleBone Black

{{% youtube 4hbY4UNFl6w %}}

So to get this going, all you need is to follow what is mentioned in the main
site and pop that sdcard into the device

    wget  http://cdimage.ubuntu.com/ubuntu-core/preview/ubuntu-core-WEBDM-alpha-02_armhf-bbb.img.xz
    unxz ubuntu-core-WEBDM-alpha-02_armhf-bbb.img.xz
    dd if=ubuntu-core-WEBDM-alpha-02_armhf-bbb.img of=/dev/sdXXX bs=32M

The alternative way is to use ubuntu-device-flash to create such image, you can get it easily for Ubuntu by adding our tools ppa

    sudo add-apt-repository ppa:snappy-dev/beta
    sudo apt update
    sudo apt install snappy-tools

And then move on to building your image, this is what I do:

    ubuntu-device-flash --verbose core --channel ubuntu-core/devel-proposed --output snappy-core.img --size 10 --developer-mode --install webdm_0.1_multi.snap --install beagleboneblack.element14_1.0_all.snap

The install option is basically an option to install `snaps` during
provisioning; you may notice this weird one
`beagleboneblack.element14_1.0_all.snap`, that is an *oem* snap and in summary,
it’s similar to the customization framework in Ubuntu Touch, but different.
today it’s pretty minimal and allows to just set the branding text either as
can be seen on the video or at the login prompt, where you would see something
like this:

    (BeagleBoneBlack)ubuntu@localhost:~$

More on the `oem` part later.

Happy sapping!
