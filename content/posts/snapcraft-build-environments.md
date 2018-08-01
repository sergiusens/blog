---
title: "Snapcraft Build Environments"
date: 2018-07-30T17:47:42-03:00
categories: [ubuntu]
tags: [snappy, snapcraft, development]
draft: false
---

# Prologue
After a week away from my computer I want to organize my thoughts on the progress made towards [build VMs](https://forum.snapcraft.io/t/status-tracking-for-build-vm/5505) by providing this write up since that forum post can be a bit overwhelming if you are casually wanting to keep up to date.

The reasons for this feature work to exist, for those not up to speed, is that we want to have a very consistent build environment for which anyone building a project can have an expectable outcome of a working snap (or non working one if it really doesn't). Case in point, we want to avoid the _works for me_ situation as much as possible.

One of the reasons to choose virtualization over containers is that we even want to isolate ourselves from the kernel running on the system, providing a consistent experience for things like the ability to use `build-snaps`.

This is an overview of the current state of the development of this functionality.

# Working on a project

## Creation
Let's try and snap up a very simple _hello world_ project written in C and driven by a `Makefile`, the `snapcraft.yaml` for this project looks like the following:

```
name: make-hello
version: 0.1
summary: say hello to the world
description: |
  This is a basic make snap. It just prints a hello world.
confinement: strict
grade: devel
base: core18

build-packages: [gcc, libc6-dev]

apps:
  make-hello:
    command: test

parts:
  make-project:
    source: .
    plugin: make
```

After this you just need to run `snapcraft` to get a resulting snap. Given that we set the `base` to `core18` a virtual machine will be setup so that the entire lifecycle process of creating that snap happens entirely in that environment.

Here's a video of what it looks like:
<script src="https://asciinema.org/a/iUi22VITeyDq5qa0XK8uDZAYB.js" id="asciicast-iUi22VITeyDq5qa0XK8uDZAYB" async></script>

As you can see there, there is essentially no contamination from the build in the project directory (keen eyes may notice a snap directory there, _that is a bug_).

You may have noticed, too, that creating the image took some time, that is the first boot (_a ticker hinting about this is in the works_) and the initial environment setup taking place (_as can be seen in the video_).

## Iteration
The first boot took some time, if this where the case every time you ran snapcraft, I don't believe you would be fond of iterating on a project. This is why we have optimized it (_more to come_) by having pre flight checks and saving the virtual machine state before we tear it down to load from a booted state.

Here is the same project where I have already run `snapcraft pull`:
<script src="https://asciinema.org/a/iXDTKxGeXUwJww7Tw4JFzmSri.js" id="asciicast-iXDTKxGeXUwJww7Tw4JFzmSri" async></script>

## Inspection
As everything is run inside the virtual machine instance, it becomes really hard to take a glance at what is going on when running something or how to debug an issue we may have. To solve that, we added a few options to the lifecycle related commands which will drop you into a shell inside the virtual machine instance, these are:

- `--shell`, which runs up to the previous lifecyle step and replaces the called out lifecycle step to be run (_in the pipeline_).
- `--shell-after`, which allows you to enter a shell after the lifecycle step has run.
- `--debug`, which drops you into a shell if there are errors in any lifecycle step related to the project.

These command options should drop you into the corresponding directory for the `part` and lifecycle step (_in the pipeline_).

The shell prompt (`PS1`) is descriptive to the project with regards to its location and should be intuitive into how to get to places.

Let's see how `--shell-after` works with our current project which last run through the build step:
<script src="https://asciinema.org/a/Ic9iflFsyLnwOVtD6zCCY870V.js" id="asciicast-Ic9iflFsyLnwOVtD6zCCY870V" async></script>

As you can see, we can even continue running snapcraft from inside the virtual machine instance from any working directory.

Debugging should be a similar experience, let's pick up from where we left off and introduce an error that will trigger dropping us into a shell inside the virtual machine:
<script src="https://asciinema.org/a/XKZcS0KISKxowBFWBAD97MZkR.js" id="asciicast-XKZcS0KISKxowBFWBAD97MZkR" async></script>

In that video, you can observe that after fixing the issue we just ran snapcraft through its entire lifecycle to get the snap created.

## Cleanup
Cleaning up is rather easy, just run `snapcraft clean`. This simply wipes the data directories reserved for the snapcraft project.

# Looking further
So far I have targets for `core18` and a fictitious `core16` base. To validate the entire process though, I did also play with creating a Fedora based disk to run through that same make project by hacking up a quick DNF repo handler inside snapcraft, the resulting snap that came out of exercising this process was uninstallable as expected as there is no fedora base (yet at least).