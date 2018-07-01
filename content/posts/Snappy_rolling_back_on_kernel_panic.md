---
Categories: [ubuntu]
Description: ""
Keywords: []
Tags: [snappy]
date: 2015-05-08T12:31:49-03:00
title: Snappy rolling back on kernel panic
---

Image you get an update and the kernel panics with that update, what are you to
do? Suppose now that you have a snappy based system, this is automatically
solved for you.

Here’s a short video showing this on a Beagle Bone Black, the concept is quite
simple, if there is a panic, we revert to the last known state. In this video I
inject an initrd that panics on boot after issuing a `snappy update` and before
rebooting into the update.

{{% youtube xIHEy5saBa8 %}}

In this video you observe the following:

- Manually checking for updates.
- Manually applying the updates.
- How the a/b boot selection is done.
- Implicitly observe the internal (*subject to change*) layout of `snappy-boot`
  and `system-a` or `system-b` selection.
- Rebooting into the new kernel.
- Observing a panic and rebooting back into the working system.

In the normal case this would seldom happen (the broken boot aside) as the
autopiloting feature is enabled by default today, which you can check by
running `snappy config ubuntu-core`
