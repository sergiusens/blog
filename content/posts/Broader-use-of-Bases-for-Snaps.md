---
title: "Broader use of bases for snaps"
date: 2019-03-08T12:18:53-03:00
tags: [snappy, snapcraft, development]
draft: false
---

We have regular *Engineering Sprints* at Canonical. These are a great opportunity for engineering teams to come together and discuss progress and roadmaps, as well as code and work together. Many of us have just returned from one such event in _Malta_, getting home just before the widely report storm hit the island (see [Destruction across Malta as gale-force winds batter islands](https://www.timesofmalta.com/articles/view/20190224/local/gale-force-winds-cause-substantial-damage-but-no-injuries-reported.702795)).:w

And one of the things we discussed over the course of that week was the implications of using *bases* when snaps use `type` other than `app` (a base is a special kind of snap that provides a minimal set of libraries common to most applications).

## Bases for more than apps

So far, the only place developers have been using bases is when they declare `type: app` in their *snap.yaml*, and it's also the implicit snap `type` when nothing is declared.

But from snapcraft's point of view, the `base` keyword can also be used with snap types of `gadget` and `kernel`, and these would then work internally inside snapd. 

Discussions at the sprint the following initial questions about using *base* with gadget and kernel snaps:

- Is it useful?
- How would everything connect, internally, inside snapd.
- How would it affect the runtime? (as a kernel or gadget snap could be _userspace_ independent, if all the potential hooks they used were architecture independent and would require a base for the cases, it would be relevant. 

As a result of these questions, for cases where a `base` is not required, we are introducing a new `base` called _none_. From a runtime perspective, this will is a real base but is has nothing in it.

But what will happen at build time? Where are builds supposed to take place? Where do we build some other snap types we haven't mentioned before, such as `base` or `snapd`?

To solve the above, we are also introducing a new keyword, `build-base`. This takes a value, such as `core18`, which becomes the build environment used to create snaps targeting, for example, `core18`.

## Status of the core16 base

With the release of Snapcraft 3.0, all new features are now tied to the use of the `base` keyword. If *base* isn't specified, your snap will build just as it used to, only without the new features. This way, we don't force anyone to use or migrate to our new features and can maintain a only single version of snapcraft, regardless of whether you're using bases or do not.

## core16 base status 

We originally planned to have a `core16` base snap to support a 16.04 base with the release of Snapcraft 3.0. However, the release of *core16* is currently behind schedule. 

To help mitigate the effects of this delay, we're going to activate a slight trick in snapcraft. The trick is that snapcraft will soon allow specifying `core` as a base but it will write out a `snap.yaml` with no base. This means that `core` essentially **is** a base internally - it's just treated differently because it's coupled with snapd (which, with the `core16` work, has also been stripped out into its own snap).

## Summary

Here's what you'll be able to do with the upcoming releases of snapcraft:

- Use `none` as a base (requires work in snapd as well).
- Specify a `build-base` when using `base: none`.
- Use `core` as a base in `snapcraft.yaml`.

And you can already use the `base` keyword for snaps of type `app`, `kernel` and `gadget`.
