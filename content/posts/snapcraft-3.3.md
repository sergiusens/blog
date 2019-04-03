---
title: "Snapcraft 3.3"
date: 2019-04-03T14:05:04-03:00
categories: [ubuntu]
tags: [snappy, snapcraft, development]
draft: true
---

snapcraft 3.1 is now available on the stable channel of the Snap Store. This is a new minor release building on top of the foundations laid out from the [snapcraft 3.3 release](https://github.com/snapcore/snapcraft/releases/tag/3.3).

If you are already on the `stable` channel for snapcraft then all you need to do is wait for the snap to be refreshed.

The full [release notes](https://github.com/snapcore/snapcraft/releases/tag/3.3) are replicated here below

## Core
### base: core
In order to use the new features of snapcraft, introduced with 3.0, and still use 16.04 as a base, support for use of `base: core` has been added. This will trigger the same semantics as using the `base` keyword in snapcraft.

### Alternate directory for snapcraft.yaml
`build-aux/snap` is now supported as an alternative directory for snapcraft.yaml and its assets (i.e.; hooks, gui, ...). To avoid confusions, snapcraft now display what directory it is picking for assets depending on where the snapcraft.yaml is found. It will only pick `build-aux/snap` for assets if the `snapcraft.yaml` is found in that path.

### string validations
Snapcraft now produces a better error for when the type detected for the version _string_ is not a _string_.

## Plugins
### python
A minor fix which should bring _rebuilding_ capabilities to projects using the **python** plugin.
Long gone should be the days of seeing messages of the following construct:
```
You must give at least one requirement to install (maybe you meant "pip install …")
```

### python and go
Expanded schema errors for users of the **go** and **python**, allowing for early discovery of non-valid uses of these plugins. Most importantly, this eliminates the cryptic error during build time when not using a combination of the `source` and `<plugin-name>-packages` keywords.

### nodejs
Entries of type _string_ are now supported in `package.json` for the `bin` keyword (previously, the plugin could only parse dictionary entries), this means that constructs from `package.json` of the form:
```json
{
  "name": "unnamed",
  "version": "1.0",
  "description": "Using string bin entries.",
  "main": "index.js",
  "bin": "bin/index.js",
}
```
should be parse and interpreted correctly by snapcraft.

## Store
### Register against a specific store
Brand stores are a commercial feature of the Snap Store.

The following syntax is now allowed as part of the `register` command:
```
snapcraft register [--store <store>] <snap-name>
```
Which will allow users to register snaps for specific brand stores.

---

The issues and features worked on for 3.3 can be seen on the [3.3](https://bugs.launchpad.net/snapcraft/+milestone/3.3) launchpad milestone which are reflected in the following change list:

#### Sergio Schvezov
* many: support for "base: core" in snapcraft.yaml (#2499) (LP: #1819290)
* python plugin: graceful ret when no packages set (#2498) (LP: #1794216)
* many: support the use of build-aux/snap (#2496) (LP: #1805219)
* nodejs pluging: support for type str bin entries (#2501) (LP: #1817553)
* store: support registering to a specific store (#2479) (LP: #1820107)
* meta: fix management of snap/local (#2502)
* tests: improve login pexpect errors
* tests: correctly retry registers
* build providers: enhance provider errors (#2508) (LP: #1821217)
* build providers: improve handling in snap logic (#2507) (LP: #1820864)
* tests: filter per arch and fix snap build deps

#### Claudio Matsuoka
* sources: handle network request errors (#2494)
* store: handle invalid snap file errors (#2492)
* tests: fix multipass error handling spread test (#2491)
* plugins: improve python and go schema validation (#2473) (LP: #1806055)
* cli: disable raven if not running from package (#2503)

#### Facundo Batista
* schema: better 'version' error messages: wrong type and incorrect length (#2497)
  (LP: #1815812)

