---
Categories: [ubuntu]
Description: ""
Keywords: [parts, development]
Tags: [snappy, snapcraft]
date: 2016-06-27T12:57:07-03:00
title: The Snapcraft Parts Ecosystem
---

Today I am going to be discussing `parts`. This is one of the pillars of
snapcraft (together with `plugins` and the `lifecycle`).

For those not familiar, this is snapcraft's general purpose landing page,
http://snapcraft.io/ but if you are a developer and have already been
introduced to this new world of snaps, you probably want to just go and hop
on to http://snapcraft.io/create/

If you go over this `snapcraft tour` you will notice the many uses of `parts`
and start to wonder how to get started or think that maybe you are duplicating
work done by others, or even better, maybe an upstream. This is where we start to
think about the idea of sharing parts and **this** is exactly what we are going
to go over in this post.

> To be able to reproduce what follows, you'd need to have snapcraft 2.12 installed.

# An overview to using remote parts

So imagine I am someone wanting to use libcurl. Normally I would write the
part definition from scratch and be on with my own business but surely I might
be missing out on something about optimal switches used to configure the
package or even build it. I would also need to research on how to use the specific
plugin required. So instead, I'll see if someone already
has done the work for me, hence I will,

```bash
$ snapcraft update
Updating parts list... |
$ snapcraft search curl
PART NAME  DESCRIPTION
curl       A tool and a library (usable from many languages) for client side URL tra...
```

Great, there's a match, but is this what I want?

```bash
$ snapcraft define curl
Maintainer: 'Sergio Schvezov <sergio.schvezov@ubuntu.com>'
Description: 'A tool and a library (usable from many languages) for client side URL transfers, supporting FTP, FTPS, HTTP, HTTPS, TELNET, DICT, FILE and LDAP.'

curl:
  configflags:
  - --enable-static
  - --enable-shared
  - --disable-manual
  plugin: autotools
  snap:
  - -bin
  - -lib/*.a
  - -lib/pkgconfig
  - -lib/*.la
  - -include
  - -share
  source: http://curl.haxx.se/download/curl-7.44.0.tar.bz2
  source-type: tar
```

Yup, it's what I want.

## An example

There are two ways to use these parts in your `snapcraft.yaml`, say this is your
`parts` section

```yaml
parts:
    client:
       plugin: autotools
       source: .
```

My `client` part which is using sources that sit alongside this `snapcraft.yaml`,
will hypothetically fail to build as it depends on the curl library
I don't yet have. There are some options here to get this going, one using `after`
in the part definition implicitly, another involving composing and last but not least
just copy pasting what `snapcraft define curl` returned for the part.

### Implicitly

The implicit path is really straightforward. It only involves making the part look
like:

```yaml
parts:
    client:
       plugin: autotools
       source: .
       after: [curl]
```

This will use the cached definition of the part and may potentially be updated by
running `snapcraft update`.

### Composing

What if we like the part, but want to try out a new configure flag or source
release? Well we can override pieces of the part; so for the case of wanting to
change the source:

```yaml
parts:
    client:
        plugin: autotools
        source: .
        after: [curl]
    curl:
        source: http://curl.haxx.se/download/curl-7.45.0.tar.bz2
```

And we will get to build curl but using a newer version of curl. The trick
is that the part definition here is missing the `plugin` entry, thereby
instructing snapcraft to look for the full part definition from the cache.

### Copy/Pasting

This path is a path one would take if they want full control over the part.
It is as simple as copying in the part definition we got from running
`snapcraft define curl` into your own. For the sake of completeness here's how it
would look like:

```yaml
parts:
    client:
        plugin: autotools
        source: .
        after: [curl]
    curl:
        configflags:
            - --enable-static
            - --enable-shared
            - --disable-manual
        plugin: autotools
        snap:
            - -bin
            - -lib/*.a
            - -lib/pkgconfig
            - -lib/*.la
            - -include
            - -share
        source: http://curl.haxx.se/download/curl-7.44.0.tar.bz2
        source-type: tar
```

# Sharing your part

Now what if you have a part and want to share it with the rest of the world? It
is rather simple really, just head over to https://wiki.ubuntu.com/snapcraft/parts
and add it.

In the case of `curl`, I would write a yaml document that looks like:

```yaml
origin: https://github.com/sergiusens/curl.git
maintainer: Sergio Schvezov <sergio.schvezov@ubuntu.com>
description:
  A tool and a library (usable from many languages) for
  client side URL transfers, supporting FTP, FTPS, HTTP,
  HTTPS, TELNET, DICT, FILE and LDAP.
project-part: curl
```

What does this mean? Well, the **part** itself is not defined on the wiki, just
a pointer to it with some meta data, the **part** is really defined inside a
`snapcraft.yaml` living in the `origin` we just told it to use.

The extent of the keywords is explained in the [documentation](https://github.com/ubuntu-core/snapcraft/blob/master/docs/snapcraft-parts.md#reusing-parts), that is an
upstream link to it.

The core idea is that a `maintainer` decides he wants to share a part. Such a
`maintainer` would add a `description` that provides an idea of what that part
(or collection of parts) is doing. Then, last but not least, the `maintainer` declares
which parts to expose to the world as maybe not all of them should. The main part
is exposed in `project-part` and will carry a top level name, the maintainer can
expose more parts from `snapcraft.yaml` using the general `parts` keyword. These
parts will be namespaced with the `project-part`.
