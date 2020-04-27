---
title: "Snapcraft 4.0"
date: 2020-04-27T12:38:02-03:00
keywords: [focal,20.04,core20,snaps,snapcraft]
draft: false
---

The team behind Snapcraft is please to present Snapcraft 4.0.
Among the many changes included, here is the summary of highlights:

-   Support for the `core20` base.
-   `--use-lxd` support for all the snap supported architectures.
-   Improved plugins for `core20`.
-   Support for adding external repositories.

This release can currently be tried out by switching to the candidate
channel for [Snapcraft](https://snapcraft.io/snapcraft).

<iframe src="https://snapcraft.io/snapcraft/embedded?button=black" frameborder="0" width="100%" height="300px" style="border: 1px solid #CCC; border-radius: 2px;"></iframe>

<a id="org2c66b6d"></a>

# Plugins V2

To support `core20`, a new plugin infrastructure was worked on, this
new infrastructure simplifies the plugins in a great way, with one
great advantage: "*quick rebuilds*".

The plugins created for `core20` have been greatly simplified to drive
the build technology and not so much to setup the environment to do
so. This simplification in plugin logic has removed most of the
perceived *magic* during Snapcraft builds for parts.

They now only are concerned with Snapcraft's *build* step. The *pull*
step is now completely owned by Snapcraft and dedicated to managing
the `source` related entries for parts.

The list of plugins that have `core20` is not as broad as `core` or
`core18`, but they are a good set of plugins that make sense for this
initial release:

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

The command line related to plugins has gained some additional
parameters to specifically reach *base* relevant information:

-   `snapcraft help <plugin-name> [--base <base>]`
-   ~snapcraft list-plugins [&#x2013;base <base>]

These commands will default to using the `base` defined in the current
Snapcraft project or to the latest supported base (i.e.; `core20`).

Plugins also now have their properties scoped (i.e.; prefixed with the
plugin-name).


<a id="orgf23feb5"></a>

## autotools

The autotools plugin for `core20` works mostly in the same way, with
the following exceptions:

-   The plugin checks for the existence of `configure` in the source and
    if not found, runs `autoconf --install`.
-   `configflags` has been renamed to `autotools~configure-parameters`.
-   `install-via` has been removed.


<a id="orgd2a3c0e"></a>

## cmake

This plugin works mostly the same, except for the fact that
`configflags` has been renamed to `cmake-parameters`.


<a id="org3291d8f"></a>

## dump

This behaves in the same way for `core20` as for `core18` or `core`.


<a id="orgf09a235"></a>

## go

The plugin has been revamped for `core20`, it only supports projects
using `go.mod`, which means it only supports version of `go` that
support this.

These are the only configuration parameters available to the plugin
when using `core20`:

-   `go-channel`.
-   `go-buildtags`.

While these are no longer available when using `core20`:

-   `go-importpath`.
-   `go-packages`.


<a id="org0b0a82c"></a>

## make

The following parameteres will be accepted by the plugin when setting
`core20` as the base:

-   `make-parameters`.

Whereas the following will not be as they can all be easily managed
with `override-build`:

-   `makefile`.
-   `artifacts`.
-   `make-install-var`


<a id="org51feefc"></a>

## meson

This plugin suffered no change for use in `core20` aside from being
enhanced for easier rebuilds.


<a id="org426a14b"></a>

## nil

This behaves in the same way for `core20` as for `core18` or `core`.


<a id="org7c77ebe"></a>

## npm

This is a new plugin being introduced in `core20`, meant to replace
with `nodejs` plugin only available for `core` and `core18`.

The only parameter the plugin now accepts is `npm-node-version`.


<a id="orgf5d5516"></a>

## python

This plugin has been the one which was simplified the most for
`core20` and yet provides the most new functionality. It essentially
behaves like a virtual environment, preferring the python interpreter
shipped in the `core20` base.

By behaving this way, the plugin now operates in a closer manner to
what a python developer would expect and allows for easier *snap*
customization whilst still using the plugin.

The plugin can use an interpreter if it is added trough a
comprehensive list of `stage-packages` (an extension shall be
evaluated in the future to provide alternative complete python
stacks).

The plugin when used with `core20` accepts the following parameters
with the same semantics as the V1 plugin used in `core` and `core18`:

-   `python-packages`.
-   `requirements`.
-   `python-packages`.


<a id="orgd96325f"></a>

## rust

This is another plugin that has been simplified in amount of
parameters when targeting `core20` as a base:

-   `rust-features` same behaviour as for `core` and `core18`.
-   `rust-path`, defaulting to the current working directory, but can be
    set to the relative path of the crate to build when using
    workspaces.


<a id="orgb37bf2a"></a>

# Package Management

This feature adds a high-level key package-management to snapcraft.yaml that
enables users to configure additional repositories & components. Specifically,
the scope of package-management is for anything affecting the behavior and
availability of:

-   build-packages
-   stage-packages
-   build-snaps
-   stage-snaps
-   python-packages

The scope of this spec will focus on the configuration of `apt` repositories,
affecting the availability of `build-packages` and `stage-pagickages`.

Simply configure `package-repositories` in snapcraft.yaml.  Snapcraft will log
a warning that the feature is experimental until the schema is to be considered
stable.

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

# Build Environments

The `--use-lxd` flag has been promoted out of experimental and now
supports the same build roots as build.snapcraft.io (or Launchpad),
bringing the two environments closer together. With these new images,
there is now support for all the snap enabled architectures too.

# Progressive Releases

Initial *experimental* support for progressive releases has landed in
Snapcraft. To view any existing progressive release use the `status`
command, as an example:

    $ snapcraft status candycane
    Track     Arch    Channel    Version    Revision    Progress
    latest    all     stable     -          -           -
    		  candidate  -          -           -
    		  beta       0.6        8           → 20%
    			     10         13          → 80%
    		  edge       ↑          ↑         -

To perform a progressive release, use the `release` command with the
with the `--progressive` option. After releasing, the status of the
release will be shown.

