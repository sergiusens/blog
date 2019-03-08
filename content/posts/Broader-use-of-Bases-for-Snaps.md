---
title: "Broader use of bases for snaps"
date: 2019-03-08T12:18:53-03:00
draft: true
---

An Engineering Sprint at Canonical is a time and place where the engineering teams at Canonical across the board gather to get things done. An event of this nature took place a couple of weeks ago in _Malta_ (side note, as reported on the news, we left just in time [Destruction across Malta as gale-force winds batter islands](https://www.timesofmalta.com/articles/view/20190224/local/gale-force-winds-cause-substantial-damage-but-no-injuries-reported.702795)).

Over the course of that week we took the opportunity to discuss the implications of bases across snap `type`s with, among other foundational people, the snapd team.

## Bases for more than apps
So far, the only real place where developers have been using bases was for snaps of `type: app`. This is also the implicit snap `type`. Even though, from snapcraft's point of view, using the `base` keyword with snap types of `gadget` and `kernel` would work internally inside snapd. 

These were the questions around use of bases for these other snaps:

- Is it useful at all?
- How everything would connect internally inside snapd.
- How this would affect the runtime as a kernel or gadget snap could be _userspace_ independent if all potential hooks they used were architecture independent and would require a base for the cases it would be relevant. So for the case where a `base` is not required, we are introducing a new `base`, called _none_. From a runtime perspective, this will be a real base, but with nothing in it.

But what happens at build time? Where are builds supposed to take place? Where do we build some other snaps I haven't mentioned before like those of `base` or `snapd`? To solve this situation, we are introducing a new keyword, `build-base` where one could use values such as `core18` and the build environment to used to create snaps targetting `core18` will be brought up. This is 

## Status of the core16 base
Since snapcraft 3.0, all new features of snapcraft are tied to the use of the `base` keyword. This has the effect of not forcing anyone onto the new features if they do not desire to do so and use the same version of snapcraft to work on snaps that use bases or do not.

For the situation of relying on a 16.04 based base, the original plan was to have the `core16` base snap ready and use that from the start.

Considering that the `core16` base is quite obviously behind schedule there is a trick we are going to play in snapcraft. The trick is that snapcraft will soon allow specifying `core` as a base but write out a `snap.yaml` with no base.

The trick is, that `core` essentially **is** a base, internally it is just treated differently as it is coupled with snapd (which with the `core16` work has also been stripped out into its own snap).

## Summary

What to expect you would be able to do in the upcoming releases of snapcraft:

- Use `none` as a base (requires work in snapd as well).
- Specify a `build-base` when using `base: none`.
- Use `core` as a base in `snapcraft.yaml`.

You can already use the `base` keyword for snaps of type `app`, `kernel` and `gadget`.