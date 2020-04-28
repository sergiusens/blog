---
title: "Snapcraft 4.0"
date: 2020-04-27T12:38:02-03:00
keywords: [focal,20.04,core20,snaps,snapcraft]
draft: false
---

The team behind Snapcraft is pleased to announce the release of 
Snapcraft 4.0. Among the many updates, fixes and additions it  
includes, the following are what we consider its highlights:

- the `core20` base is now supported
- `--use-lxd` can now be used with all snap supported architectures
- improved plugins for `core20`
- support for adding external repositories

To try this release, switch to the candidate channel for
[Snapcraft](https://snapcraft.io/snapcraft).

<iframe src="https://snapcraft.io/snapcraft/embedded?button=black" frameborder="0" width="100%" height="300px" style="border: 1px solid #CCC; border-radius: 2px;"></iframe>

<a id="org2c66b6d"></a>

## Plugins V2

New plugin infrastructure has been developed to support `core20`.

This new infrastructure greatly simplifies plugins and delivers another 
great advantage: "*quick rebuilds*".

Thanks to `core20` plugins being much simpler, so to is the environment
setup process, removing most of the perceived *magic* during Snapcraft
builds for parts.

Plugins are now applicable only to Snapcraft's *build* step. The *pull*
step, for instance, has become completely owned by Snapcraft and
dedicated to managing the `source` related entries for parts.

The following plugins have been updated to work with `core20`:

-   [autotools](#orgf23feb5)
-   [cmake](#orgd2a3c0e)
-   [dump](#org3291d8f)
-   [go](#orgf09a235)
-   [make](#org0b0a82c)
-   [meson](#org51feefc)
-   [nil](#org426a14b)
-   [npm](#org7c77ebe)
-   [python](#orgf5d5516)
-   [rust](#orgd96325f)

While the list of plugins is not as broad as for `core` or `core18`,
they offer a strong foundation for the majority of snaps, and the list
will grow after this initial release.

The command line related to plugins has gained some additional
parameters to specifically reach *base* relevant information:

-   `snapcraft help <plugin-name> [--base <base>]`
-   ~snapcraft list-plugins [&#x2013;base <base>]

These commands will default to using the `base` defined in the current
Snapcraft project or to the latest supported base (i.e., `core20`).

Also, plugins now have their properties scoped (i.e.; prefixed with the
plugin-name).

<a id="orgf23feb5"></a>

### autotools

The autotools plugin for `core20` works mostly in the same way, with
the following exceptions:

- the plugin checks for the existence of `configure` in the source. If
  not found, `autoconf --install` is executed instead
- `configflags` has been renamed to `autotools~configure-parameters`
- `install-via` has been removed

<a id="orgd2a3c0e"></a>

### cmake

This plugin works mostly the same, except for the fact that
`configflags` has been renamed to `cmake-parameters`.


<a id="org3291d8f"></a>

### dump

This behaves in the same way for `core20` as for `core18` or `core`.


<a id="orgf09a235"></a>

### go

The plugin has been revamped for `core20`. It now only supports projects
using `go.mod`, which means it only supports version of `go` that
support this.

The following are the only configuration parameters available to the plugin
when using `core20`:

- `go-channel`
- `go-buildtags`

These options are no longer available when using `core20`:

- `go-importpath`
- `go-packages`

<a id="org0b0a82c"></a>

### make

The following parameters will be accepted by the plugin when setting
`core20` as the base:

-   `make-parameters`

The following are no longer accepted but should instead be easily managed
with `override-build`:

-   `makefile`
-   `artifacts`
-   `make-install-var`


<a id="org51feefc"></a>

### meson

This plugin works the same in `core20` although it has been enhanced
for easier rebuilds.


<a id="org426a14b"></a>

### nil

This plugin behaves in the same way for `core20` as for `core18` or `core`.


<a id="org7c77ebe"></a>

### npm

This is a new plugin for `core20`. It is intended to replace
the `nodejs` plugin, which is only available for `core` and `core18`.

The only parameter the plugin now accepts is `npm-node-version`.


<a id="orgf5d5516"></a>

### python

The python plugin has been simplified the most for `core20` and yet 
provides the most new functionality. It essentially behaves like a
virtual environment, preferring the python interpreter shipped in
the `core20` base.

By behaving this way, the plugin operates more like how a Python
developer would expect, allowing for easier *snap*customization
whilst still using the plugin.

The plugin can use an interpreter if it is added through a
comprehensive list of `stage-packages` (an extension shall be
evaluated in the future to provide alternative complete python
stacks).

When used with `core20`, the plugin accepts the following parameters,
with the same semantics as the V1 plugin used in `core` and `core18`:

- `python-packages`
- `requirements`
- `python-packages`


<a id="orgd96325f"></a>

### rust

This is another plugin that has been simplified to reduce the number
of parameters when targeting `core20` as a base:

- `rust-features` same behaviour as for `core` and `core18`
- `rust-path`, defaulting to the current working directory, but can be
  set to the relative path of the crate to build when using workspaces


<a id="orgb37bf2a"></a>

## Package Management

This feature adds high-level package-management to snapcraft.yaml, enabling
users to configure additional repositories & components.

Specifically, the scope of package-management is for anything affecting the
behavior and availability of:

-   build-packages
-   stage-packages
-   build-snaps
-   stage-snaps
-   python-packages

The scope of this spec will focus on the configuration of `apt` repositories,
affecting the availability of `build-packages` and `stage-packages`.

To use, simply configure `package-repositories` in snapcraft.yaml.

Note: snapcraft will log an 'experimental feature' warning until the 
schema is  considered stable.

Here are some example configurations:

    name: apt-example
    base: core18
    
    <snip>
    
    package-repositories:
      - type: apt
        ppa: snappy-dev/snapcraft-daily
    
      - type: apt
        deb-types: [deb, deb-src]
        components: [main]
        suites: [$SNAPCRAFT_APT_RELEASE]
        key-id: 78E1918602959B9C59103100F1831DDAFC42E99D
        url: http://ppa.launchpad.net/snappy-dev/snapcraft-daily/ubuntu
    
      - type: apt
        deb-types: [deb, deb-src]
        name: default
        components: [main, multiverse, restricted, universe]
        suites: [$SNAPCRAFT_APT_RELEASE, $SNAPCRAFT_APT_RELEASE-updates]
        key-id: test-key
        url: http://archive.ubuntu.com/ubuntu


<a id="org325e09b"></a>

## Build Environments

The `--use-lxd` flag has been released from its experimental phase and
now supports the same build roots as build.snapcraft.io (or Launchpad),
bringing the two environments closer together. With these new images,
there is now support for all the snap enabled architectures too.

## Progressive Releases

Initial *experimental* support for progressive releases has landed in
Snapcraft. To view any existing progressive release use the `status`
command, as an example:

    $ snapcraft status candycane
    Track     Arch      Channel    Version    Revision    Progress
    latest    all       stable     -          -           -
    		            candidate  -          -           -
    		            beta       0.6        8           → 20%
    			                   10         13          → 80%
    		            edge       ↑          ↑         -

To perform a progressive release, use the `release` command with the
with the `--progressive` option. After releasing, the status of the
release will be shown.
