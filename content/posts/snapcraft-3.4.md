---
title: "Snapcraft 3.4"
date: 2019-04-19T16:32:17-03:00
draft: false
---

I am really excited about this release as it brings two features I have personally
been missing:

- the option to `--use-lxd`
- the possibility to `snapcraft try`

If you are already on the `stable` channel for snapcraft then all you need to do is wait for the snap to be refreshed.

The full [release notes](https://github.com/snapcore/snapcraft/releases/tag/3.4) are replicated here below

---

## snapcraft snap
The snapcraft snap has now moved to using `base: core`, this unlocks snapcraft into using the snapcraft 3.0 features in its own `snapcraft.yaml` in order to build snapcraft from snapcraft (_lots of snapcraft in that sentence!_)

## Core

### New build provider support: LXD

You can now use LXD as a build provider, operations supported by the default mode (using multipass) are also supported with LXD, such as:

- `--shell`
- `--shell-after`
- `--debug`

To trigger use of LXD, the snapcraft lifecycle commands (`pull`, `build`, `stage` and `prime`, together with `clean`) need use the `--use-lxd` option.

This feature is introduced early to get feedback, but is not production ready unless it is a discardable CI environment. Early adopters may see future (with no automatic migration) changes to:

- storage setups
- default profiles
- use of LXD projects

### snapcraft try

By default, when triggering builds in a clean environment, it is sometimes desirable to have the `prime` directory locally to be able to `snap try prime`. For this reason `snapcraft try` will run through the lifecycle all the way to `prime`(if not run before) and offer the `prime` directory locally.

> While this version of snapcraft is not on stable, when combined with `--use-lxd`, one must pass `SNAPCRAFT_BUILD_ENVIRONMENT_CHANNEL_SNAPCRAFT=latest/candidate` to snapcraft. Support for snap injection for LXD will be worked on when snapd 2.38 is released.

## Plugins
### go
The go plugin now works more broadly when using `classic` confinement. This should avoid the necessity of specifying `no-patchelf` for parts failing to patch correctly.

### catkin
The catkin plugin has been enhanced to support stage-snaps to satisfy dependencies, a detailed write up can be found on the [snapcraft blog](https://snapcraft.io/blog/speed-up-your-ros-snap-builds).

---

The issues and features worked on for 3.4 can be seen on the [3.4](https://bugs.launchpad.net/snapcraft/+milestone/3.4) launchpad milestone which are reflected in the following change list:

####  Sergio Schvezov
* build providers: modify the _run signature (#2511) (LP: #1821401)
* build providers: support for provider setup (#2515) (LP: #1821586)
* readme: add snap store badge (#2516)
* build providers: initial support for LXD (#2509) (LP: #1805221)
* cli: cleanup environment detection (#2521)
* build providers: add API for friendly instance type names (#2522)
* snap: set core as a base (#2520)
* ci: improve travis integration conditionals (#2523)
* cli: snapcraft try (#2524) (LP: #1805212)
* build providers: idempotent destroy for LXD (#2529)
* tests: add missing pylxd Build-Depends
* tests: restrict catking stage-snap tests arches

#### Claudio Matsuoka
* repo: handle deb package fetch error (#2513)
* project: ensure yaml load returns a dictionary (#2517)
* many: better handling of appstream icons (#2512) (LP: #1814898)
* go plugin, elf: use patchelf 0.10 and relink dynamic go binaries (#2519)
  (LP: #1805205)
* snap: use snapcraft's 0.10 patchelf branch (#2528)
* snap: revert to patchelf 0.9 with local patches (#2531)

#### adanhawth 
* schema: add more detail wrt numeric version errors (#2506)

#### Kyle Fazzari
* catkin plugin: check stage-snaps for ROS dependencies (#2525)