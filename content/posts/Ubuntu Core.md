---
Categories: ["ubuntu"]
Description: "A snappy system"
Keywords: ["ubuntu", "snappy"]
Tags: ["ubuntu", "snappy"]
date: 2014-12-10T18:15:38+02:00
title: Ubuntu Core
Draft: false
---

Ubuntu Core is what we've been working on this past time, it has been an
interesting ride. It was developed completely in the open, there was just no
real promotion about it until we were ready.

If you had noticed, we use `ubuntu-device-flash` to create this core image, and
for development we used it across the board with the *core* subcommand. We did
learn a couple of things from the phone and decided to just provide a static
image that we could make sure would work for everyone giving it a try (aka more
QA). In essence you can still upgrade and if something is not to your like,
just rollback, it's that neat. So in summary, `ubuntu-device-flash` today is
just a step in the release process to get to the final image.

Yesterday I played around with creating a *snap* for
[camlistore](https://camlistore.org) and it was a *breeze*, To get it just
`snap install camlistore`, all the command line tools are in there provided by
the `binary` stanza from
[package.yaml](http://developer.ubuntu.com/snappy/packaging-format-for-apps/).
The `camlistored` daemon is created in the `services` list where I just needed
to provide a `start`, which in the background creates a `systemd` unit.

The beauty here is that I don't really need to know much of the underlying
technology, and that is awesome for just quickly creating a *snap*.

What is missing here though, is an easy way to configure the package that was
just intalled, for now, it should be as easy to look at the
[file system layout](http://developer.ubuntu.com/snappy/filesystem-layout/) and
going to `/var/lib/apps/<app-name>/<version>/` which would be
`/var/lib/apps/camlistore/0.8` and within we'd have
`.config/camlistore/server-config.json`, in most cases you'd want to setup your
authentication in there.

And here's the mandatory screenshot of this running on my kvm instance:

<img src="/img/camlistore00.png">
