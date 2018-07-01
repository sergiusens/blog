---
Categories: [ubuntu]
Description: ""
Keywords: [kernel]
Tags: [snappy, snapcraft]
date: 2016-03-21T21:06:57-03:00
title: Snapcrafting a kernel
---

# Introduction

With [snapcraft 2.5](https://launchpad.net/snapcraft/+milestone/2.5) which can
be installed on the upcoming 16.04 Xenial Xerus with apt or consumed from
the [2.5 tag](https://github.com/ubuntu-core/snapcraft/releases/tag/2.5) on
github we have included two interesting plugins: `kbuild` and `kernel`.

The `kbuild` plugin is interesting in itself, but here we will be discussing
the `kernel` plugin which is based out of the `kbuild` one.

A note of caution though, this `kernel` plugin is still not considered
production ready. This doesn't mean you will build kernels that don't work on
today's version of Ubuntu Core; but caution is required as the nature of
rolling, which is what this `kernel` plugin targets, can still change.
Additionally we may still modify the plugin's options for the part setup
itself.

Last but not least we are introducing, given the nature of kernel building,
some experimental cross building support. The reason for this is that cross
compiling a kernel is well understood and straightforward.

# Walkthrough

## Objective

The final objective is to obtain a kernel snap; we will want to create a kernel
that would work on the 410c DragonBoard from Arrow which features Qualcomm's
Snapdragon 410. To do so we will take a look at the
[96boards wiki](https://github.com/96boards/documentation/wiki/DragonBoard%E2%84%A2-410c-RPB-Debian-Build-Source-16.03)
and the [96boards published kernel](https://github.com/96boards/linux).

## Setup

You must be running from a Xenial Xerus system and have at least snapcraft 2.5
installed, make sure by running:

```shell
$ snacraft -v
2.5
```

If not, then:

```shell
$ apt update
$ apt install snapcraft
```

## Cloning the kernel

Since the kernel is the main project and to iterate quickly it makes sense
to clone it and start snapcrafting from there, so let's clone

```shell
git clone --depth 1 https://github.com/96boards/linux
```

Depending on when you do this, you might need to also cherry pick
[6113222fa5386433645c7707b4239a9eba444523](http://kernel.ubuntu.com/git/ubuntu/ubuntu-xenial.git/commit/?id=6113222fa5386433645c7707b4239a9eba444523)

## Creating the base snapcraft.yaml

Go into the recently cloned kernel directory and let's get started with a yaml
that has the standard entries for someone familiar with `snapcraft.yaml`:

```yaml
name: 96boards-kernel
version: 4.4.0
summary: 96boards reference kernel with qualcomm firmware
description: this is an example on how to build a kernel snap.
```

Now this is a `kernel` snap, so let's add that information in; this is rather
important since if not done, the resulting snap might as well be some sort
of asset holder; by adding the `type` of snap, *snappy Ubuntu Core* will know
what to do:

```yaml
name: 96boards-kernel
version: 4.4.0
summary: 96boards reference kernel with qualcomm firmware
description: this is an example on how to build a kernel snap.
type: kernel
```

That's all we need with regards to headers.

## Adding parts

### kernel

So let's add some parts, the first part will use the new `kernel` plugin,
This plugin's help can be seen by running:

```shell
snapcraft help kernel
```

The `kernel` plugin is based out of the `kbuild` one, so there are some extra
parameters we can use from that plugin which can be seen by running:

```shell
snapcraft help kbuild
```

And finally these plugins make use of snapcraft's source helpers which can be
discovered by runnning:

```shell
snapcraft help sources
```

So when we look at the wiki again we will notice there are 2 defconfigs,
`defconfig` and `distro.conf`.
Even though `distro.config` defines `squashfs` support to be
built as a module, let's make use of `kconfigs` and explicitly set it (we
also set a couple of other kernel configurations). We will build 2 device trees
making use of `kernel-device-trees`. In `kernel-initrd-modules` we will
mention `squashfs` as we need support for it to boot.

Given that particular piece of information let's work on adding this part:

```yaml
name: 96boards-kernel
version: 4.4.0
summary: 96boards reference kernel with qualcomm firmware
description: this is an example on how to build a kernel snap.
type: kernel

parts:
        plugin: kernel
        source: .
        kdefconfig: [defconfig, distro.config]
        kconfigs:
            - CONFIG_LOCALVERSION="-96boards"
            - CONFIG_DEBUG_INFO=n
            - CONFIG_SQUASHFS=m
        kernel-initrd-modules:
            - squashfs
        kernel-image-target: Image
        kernel-device-trees:
            - qcom/apq8016-sbc
            - qcom/msm8916-mtp
```

### firmware

To run this kernel on the DragonBoard we will need to get some firmware from
Qualcomm, so head over to https://developer.qualcomm.com/download/db410c/linux-board-support-package-v1.2.zip
and get the zip file. Extract the firmware tarball from inside that zip and
create a firmware part:

```yaml
name: 96boards-kernel
version: 4.4.0
summary: 96boards reference kernel with qualcomm firmware
description: this is an example on how to build a kernel snap.
type: kernel

parts:
    kernel:
        plugin: kernel
        source: .
        kdefconfig: [defconfig, distro.config]
        kconfigs:
            - CONFIG_LOCALVERSION="-96boards"
            - CONFIG_DEBUG_INFO=n
            - CONFIG_SQUASHFS=m
        kernel-initrd-modules:
            - squashfs
        kernel-image-target: Image
        kernel-device-trees:
            - qcom/apq8016-sbc
            - qcom/msm8916-mtp
    firmware:
        plugin: tar-content
        source: firmware.tar
        destination: lib/firmware
```

## Building

Now that we have a complete `snapcraft.yaml` we will proceed to build. If you
did this on a 64bit system, you will be able to cross compile this snap, just
run:

```shell
$ snapcraft --target-arch arm64
```

This build will take a while, an average of 30 minutes give or take. You will
eventually see a message that says `Snapped 96boards-kernel_4.4.0_arm64.snap`.
That means you are done and have successfully created a kernel snap.
