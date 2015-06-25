---
Categories: ["ubuntu"]
Description: ""
Keywords: []
Tags: ["ubuntu", "snappy"]
date: 2015-04-20T16:29:39-03:00
title: Updates to snappy and ubuntu-device-flash
Drafs: false
---

The past few weeks in the snappy world have been a revolt, better said a rapid evolution for
it to be closer to what we wanted it to be.

Some things have change, if you are tracking the bleeding edge you will notice a couple of
changes, the store for example now exposes packages depending on the *OS release*, and system
images are now built against an *OS release* as well. For core images we have two choices:

- 15.04
- rolling

`15.04` will be nicely locked down and guarantee stability while `rolling` will just roll
on and you will see it stumble over (although it shouldn’t break badly, `API`s are what we
will try and aspire to keep in the breaking zone). Try is a strong word, which is why `channels`
are being used; the `core` images have the concept of `channel` which can be:

- stable
- rc
- beta
- alpha
- edge

Today, as of this writing, we are supporting `edge` and `alpha` for each *OS release* and as soon
as we release we will have a `stable` channel enabled. Store support for channels is coming to 
a future near you which means that eventually packages can track different channels.

Another addition is a new `snap` type called `oem`, this snappy package allows OEMs to enable
devices and boards with a degree of customization such as:

- preinstalled unremovable or removable packages
- default configurations for preinstalled packages and ubuntu-core
- lock down configurations.
- custom DTBs
- boot files (e.g.; u-boot, uEnv.txt)

This package, uploaded to the store allows people to create custom *enablements* to support
their product stories. This package’s capabilities can grow in the future to support some
other niceties.

If you happen to use the development ppa for snappy `ppa:snappy-dev/tools` you should be seeing
a new `ubuntu-device-flash` in the updates which supports most of this syntax and retires early
enablement work.

So in order to create a default image for the Beagle Bone Black you would do:

    sudo ubuntu-device-flash core 15.04 --channel edge --oem beagleblack --output bbb.img

To create an generic amd64 image

    sudo ubuntu-device-flash core 15.04 --channel edge --output x86.img

`15.04` could be replaced with rolling and today the default channel is `edge` but will be
`stable` as soon as we have something in there :-)

Keep in mind now that `15.04` and `rolling` will return different store search results depending
on what the developer has targetted.

Installing local `oem` snaps passing in `--oem` forces you to setup `--developer-mode` if
the package is not signed by the store.

Last but not least, the `flashassets` entry from `device` tarballs used to enable new devices are
now ignored in favor of using the information from the `oem` snappy package, this means that if
you have a port you will need to move it over to the [oem packaging](http://bazaar.launchpad.net/~snappy-dev/snappy/snappy/view/head:/docs/oem.md).
