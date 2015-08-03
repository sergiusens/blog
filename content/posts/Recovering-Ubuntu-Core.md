---
Categories: ["ubuntu"]
Description: ""
Keywords: ["ubuntu", "snappy", "grub", "recovery"]
Tags: ["ubuntu", "snappy"]
date: 2015-08-01T19:36:12-03:00
title: Recovering Ubuntu Core
---

# Introduction

Ubuntu Core was released over half a year ago using this nice thing called 
**snappy**, the design allows for transactional updates among others, these
updates keep on *rolling* though their streams and can be kicked out (rolled
back) if something was fishy, guaranteeing a certain level of confidence that
the system should not break.

Now introduce the concept of storage, that thing that will always limit you
no matter the amount; with this consider that a popular method to avoid this
is to garbage collect, old things you forgot about will just go away.

To add a twist to the story, imagine you want to wipe your system. Given the
fact that we have a clear separation of what is writable and what is intrinsic
to what makes up the core, this is rather trivial. This would indeed reset any
customization done to the system, but...

The OS parts that compose Ubuntu Core are also garbage collected, better said 
it is like a round robin of size 2, these parts of the system are implicitly 
garbage collected so if I want to do a **real** factory reset, there is no way
to do that today because you don't have the **core** part of the system that
the device came with.

There's a couple more questions:

- do we want to update the boot loader of the running system? 
- can we recovery from a completely broken system in an autonomous way?
- how do we make this generic?

There are more...

# Booting Ubuntu Core

We use two boot loaders to power this snappy system, one is `grub`, the other 
`u-boot`. We default to the former for x86 based systems while the latter we
use for arm.

Both are similar, using an A/B model to boot with some try variables that the
boot loader in question reads to determine which system to boot and where to
rollback to in case of an issue.

The OS part of the system lives in either a partition labeled `system-a` or
`system-b`, this is similar to the Ubuntu `rootfs` with the booting parts
stripped out to a `platform` specific part that lives in the `system-boot`
partition with an A/B scheme. Take note that the `platform` name and full
functionality is under (re) design, and also currently known as `kernel` or
`device`.

All snappy packages are intended to be real snaps, today these are snappy 
package types:

- `app`
- `framework`
- `oem` (to be repurposed as `gadget`)

As mentioned, two more package types are arriving, `platform` and `os` which 
today are driven by [system image](https://wiki.ubuntu.com/ImageBasedUpgrades) 
pending a migration to actual snappy packages.

# Bootloader

There is only one boot loader core that takes care of booting into the right
system, updating this boot loader logic adds risk as breaking it would render
a system useless. As long as it's not updated everything should be fine.

## Regular booting

When business is as usual, the boot loader will load its environment, read the
`snappy_ab` variable, which would contain a value of either `a` or `b` together 
with the `snappy_mode` variable which would contain the value of `regular`.

If `snappy_ab` were to have the value `a`, the kernel `cmdline` would contain an
entry with `root=LABEL=system-a` (it's `root=LABEL=system-$snappy_ab`) whilst
the kernel part (for `grub`) would start with something like 
`linux /a/vmlinuz`..., the `initrd` line for `grub` would be rather similar
`initrd /a/initrd.img` 

## Booting into an upgrade

When the system updates the `os` and `platform` parts of the system, if the 
system was currently running from `system-a` it would drop the update onto
`system-b` and the `b` part of the `system-boot` partition for the kernel, 
initrd and related files. The `snappy_mode` variable would be set to `try`
and after the system finished booting it would set the mode to `regular` and
life would move on.

# The experiment

In this experiment we want to have a `recovery` partition with its own boot
loader and the original image that was put on the system by the manufacturer.
This would allow:

- for potential updates to the running systems.
- a mechanism for a real factory wipe.
- a tentative installer.

For this, two new components are needed:

- a better `ubuntu-device-flash` (call it `uflash`).
- a `recovery` component.

Additionally, and this is not final or has been discussed, we created some stub 
`platform` and `os` snappy packages. The `os` snap is an ubuntu rootfs put into
a `squashfs` while the `platform` snap provides the kernel, a modified `initrd`
that knows how to go into recovery or a running system and some boot loader
assets (initial `grub.cfg`).

The `recovery` component lives in the ubuntu rootfs.

For simplicity the focus was on `grub`, `gpt` and `pc-bios`.

## Creating an image.

To create an image in this experiment one would do:

    sudo ./uflash \
    --platform platform_rolling1_all.snap \
    --os ubuntu_rolling1_amd64.snap \
    --gadget generic-amd64_1.4_all.snap \
    --snaps  webdm.canonical_0.9_multi.snap \
    core.img

This would create an image called `core.img`, with 2 partitions:

- `grub`, for grub's `boot.img`
- `recovery`, with all those snappy packages passed in the command line and
  a grub's `core.img`.
 
I want to take into account again that this `uflash` thing is just for play 
and its cli will likely be different if it comes to fruition.

## Recovering

The `grub.cfg` put into `recovery` would boot into `recovery` using the 
`platform` and `os` snaps to drive it. The `recovery` logic would create two
new partitions:

- `boot`
- `writable`

It will then set up `boot` to have an A/B schema and insert the `platform` and 
`os` snappy packages used in the `recovery` partition.

All the snappy packages passed in with `--snap` in `uflash` will be installed
onto the system (depending on restraints defined in the `gadget` snap which
are ignored here as it's not part of the current experiment).

It will also install a `core.img` which the boot loader in `recovery` would 
jump to providing boot loader independence for the running system.

## Try it

- Download `core.img.xz` from 
[recovery](http://people.canonical.com/~sergiusens/snappy/recovery)
- Make sure the checksum is correct.
- Extract into `core.img`
- Run it `kvm -m 1500 core.img`

## Take away

All this is experimental, but everything used here can be found in 
[recovery](http://people.canonical.com/~sergiusens/snappy/recovery) and the 
code is composed of multiple branches under my name which can be found in
[snappy](https://code.launchpad.net/~sergiusens/snappy).

There is no indication of this merging into the main branch or product and
the code is not production quality (yet).

A side benefit is that the `recovery` logic could potentially serve as an
installer.

There are some things you just can't do with this image, which if paying 
attention would be easy to spot but just in case, system image updates won't
work.

That's all
