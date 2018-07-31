---
title: "Snapcraft Build Environments"
date: 2018-07-30T17:47:42-03:00
categories: [ubuntu]
tags: [snappy, snapcraft, development]
draft: true
---

# Prologue
After a week away from my computer I want to organize my thoughts on the progress made towards [build VMs](https://forum.snapcraft.io/t/status-tracking-for-build-vm/5505) by providing this write up.

The reasons for going down this path, for those not up to speed, is that we want to have a very consistent build environment, for which anyone building a project can have an expectable outcome of a working snap (or non working one if it really doesn't). Case in point, we want to avoid the _works for me_ situation as much as possible.

The reasons to chose virtualization over containers are that we even want to isolate ourselves from the kernel running on the system, providing a consistent experience for things like the ability to use `build-snaps`.

# Executive summary
The forum post I linked to is a bit hard to follow if you are not in the day to day of this, so let's cover the basics with a short demo,

<script src="https://asciinema.org/a/l0YhTZgw5fOeKiS7X0vpFc95w.js" id="asciicast-l0YhTZgw5fOeKiS7X0vpFc95w" async></script>

This is a simple project using a `Makefile` to build, the `snapcraft.yaml` is rather straight forward:

```
name: make-hello
version: 0.1
summary: test the make plugin
description: |
  This is a basic make snap. It just prints a hello world.
  If you want to add other functionalities to this snap, please don't.
  Make a new one.
confinement: strict
grade: devel
base: core18

build-packages: [gcc, libc6-dev]

apps:
  make-hello:
    command: test

parts:
  make-project:
    plugin: make
    source: .
```

What triggers the use of the VM to build is the use of the `base` keyword and it should be as transparent as that.

# Mechanisms

## Creation
The internals are such that when `base` is used, the following happens:

- a disk image which is meant to build for the selected base is downloaded (and cached).
- a qcow2 disk is created specifically for that project using the downloading disk image as its backing store.
- a cloud disk is created to add a user to build with, with a project specific ssh key.
- On boot the platform specific repository is refreshed (i.e.; `apt`, `dnf`, `zypper`)
- The project is mounted inside the virtual machine instance.
- If running on a host with the snapcraft snap installed, the snap and its _base_ are injected into the virtual machine instance, if not, snapcraft would be installed from within the instance.
- The snapcraft command (lifecycle steps: `pull`, `build`, `stage`, `prime` or `snap`) is executed inside the virtual machine instance. All project assets are created inside the virtual machine and written to the project's assigned disk.
- If the `snap` target was called, the resulting snap will end up in the project directory.
- Before teardown, the virtual machine state is saved.

## Iteration

Given that the virtual machine state is saved before tearing down, iterating is much faster:

- If a matching CPU/RAM state is found, that state will be loaded.
- The project is mounted inside the virtual machine instance.
- The snapcraft snap and its _base_ will only be injected if the revision has changed on the since the last run.
- The snapcraft command (lifecycle steps: `pull`, `build`, `stage`, `prime` or `snap`) is executed inside the virtual machine instance. All project assets are created inside the virtual machine and written to the project's assigned disk.
- If the `snap` target was called, the resulting snap will end up in the project directory.
- Before teardown, the virtual machine state is saved.

This workflow removes direct access to the _host_ and things like inspecting or debugging become a bit more foreign. To ease that process and make things discoverable you can now run these together with any lifecycle command:

- `--shell`, which runs up to the previous lifecyle step and replaces the called out lifecycle step to be run.
- `--shell-after`, which allows you to enter a shell after the lifecycle step has run.
- `--debug`, which drops you into a shell if there are errors in any lifecycle step relate to the project.

These command options should drop you into the corresponding directory for the `part` and lifecycle step (still needs implementing to this date).

The shell prompt (`PS1`) is descriptive to the project with regards to its location and should be intuitive into how to get to places.

## Cleanup

Cleaning up is rather easy, just run `snapcraft clean`. This simply wipes the qcow2 images and related keys.

# Experimentation

So far I have targets for `core18` and a fictitious `core16` base. To validate the entire process though, I did also play with create a Fedora based disk to run through that same make project by hacking up a quick DNF repo handler inside snapcraft, this of course was uninstallable as there is no fedora base (yet at least).