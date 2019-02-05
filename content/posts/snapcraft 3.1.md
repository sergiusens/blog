---
title: "Snapcraft 3.1"
date: 2019-02-05T14:48:31-03:00
categories: [ubuntu]
tags: [snappy, snapcraft, development]
draft: false
---

snapcraft 3.1 is now available on the stable channel of the Snap Store. This is a new minor release building on top of the foundations laid out from the [snapcraft 3.0 release](https://github.com/snapcore/snapcraft/releases/tag/3.0).

If you are already on the `stable` channel for snapcraft then all you need to do is wait for the snap to be refreshed.

The full [release notes](https://github.com/snapcore/snapcraft/releases/tag/3.1) are replicated here below

## Build Environments

It is now possible, _when using the `base` keyword_, to once again clean parts. To do so run:

```
snapcraft clean <part-name>
```

Cleaning individual steps from a specific part, previously provided by the `--step` option to `clean`, is being redesigned to be more intuitive and straight forward in its use.

## Core

### before and after
The `before` and `after` keywords, used for ordering service launching within a snap, are now properly supported in `snapcraft`.

### Appstream extractor
The appstream metadata extractor can now properly handle tags inside the relevant nodes and properly filter `xml:lang`, such that:

```xml
  <description>
    <p>List:</p>
    <p xml:lang="es">Lista:</p>
    <ul>
      <li>First item.</li>
      <li xml:lang="es">Primer item.</li>
      <li>Second item.</li>
      <li xml:lang="es">Segundo item.</li>
    </ul>
  </description>
```

Is presented as the following description in `snap.yaml`:
```
List:

- First item.
- Second item.
```

Additionally, desktop files are now properly found from the appstream `launchable` entries or by falling back to legacy mode and inferring the desktop file from the appstream `id`.

## Plugins

### cmake
The plugin can now leverage `build-snaps` into the build environment, when any given `build-snaps` entry exists for a part that uses the `cmake` plugin, the plugin will make use of `CMAKE_FIND_ROOT_PATH` so that libraries and headers from that snap are preferred.

Additionally, `cmake` primitives are now used to drive the build instead of just calling `make`.

These features have already been used to create an initial set of KDE applications leveraging `core18` as a base as described on the [KDE apps at the snap of your fingers](https://snapcraft.io/blog/kde-apps-at-the-snap-of-your-fingers) article.

### rust
The `rust` plugin has been refactored in a backwards compatible way to work better with the non-legacy `rustup` tool.

## Platforms

### OS X
When using `snapcraft` with homebrew for the first time, if `multipass` is not found, the user will be prompted to install it before proceeding.

## 

The issues and features worked on for 3.1 can be seen on the [3.1 launchpad milestone](https://launchpad.net/snapcraft/+milestone/3.1) which are reflected in the following change list:

- cmake plugin: use native primitives (#2397)
- cmake plugin: use build snaps to search paths (#2399)
- static: update to the latest flake8 (#2420)
- project: state file path change (#2419)
- tests: do not use `bash` as a reserved package name on staging (#2423)
- nodejs plugin: fail gracefully when a package.json is missing (#2424)
- tests: use fixed version for idna in plainbox (#2426)
- tests: remove obsolete snap and external tests (#2421)
- snap: re-add pyc files for snapcraft (#2425)
- tests: increase test timeout for plainbox (#2428)
- lifecycle: query for multipass install on darwin (#2427)
- cli: fix usage string in help command (#2429)
- repo: document package purpose (#2390)
- extractors: better appstream support for descriptions (#2430)
- tests: re-enable spread tests on gce
- rust plugin: refactor to use the latest rustup
- tests: temporarily disable osx tests
- snap: add build-package for xml
- appstream extractor: properly find desktop files
- appstream extractor: support legacy launchables
- snap: add xslt dependencies for lxml
- repo,baseplugin: support trusting repo keys (#2437)
- schema: allow before and after (#2443)
- meta: make hooks executable instead of complaining they are not (#2440)
- build providers: remove SIGUSR1 signal ignore workaround for multipass (#2447)
- cli: enable cleaning of parts (#2442)
- tests: appstream unit tests are xenial specific
- tests: skip rust unit tests on s390x
- tests: use more fine grained assertions in lifecycle tests
- tests: remove rust revision testing for i386
