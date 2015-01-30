---
Categories: [“ubuntu”]
Description: ""
Keywords: [“ubuntu”, “snappy”, “iot”, “things”, “ubuntu-device-flash”]
Tags: [“ubuntu”, “snappy”, “ubuntu-device-flash”]
date: 2015-01-30T11:25:45-03:00
title: Preliminary support for dtb override from OEM snaps
---

Today the always *in motion* ppa `ppa:snappy-dev/tools` has landed support for
overriding the dtb provided by the platform in the device part with one
provided by the oem snap.

The `package.yaml` for the oem snap has been extended a bit to support this,
an example follows for extending the *am335x-boneblack* platform.

<pre><code class=”yaml”>
name: mydevice.sergiusens
vendor: sergiusens
icon: meta/icon.png
version: 1.0
type: oem

branding:
    name: My device
        subname: Sergiusens Inc.

        store:
            oem-key: 123456

            hardware:
                dtb: mydtb.dtb
</code></pre>

The path `hardware/dtb` key in the `yaml` holds a value which is the path to
the dtb withing the package, so in this case, I put mydtb.dtb in the root of
the snap.

After that it’s just a `snappy build` away:

    snappy build .

In order to get this properly provisioned, first we need the latest
`ubuntu-device-flash` from the `ppa:snappy-dev/tools`, so let’s get it

    sudo add-apt-repository ppa:snappy-dev/tools 
    sudo apt update
    sudo apt install ubuntu-device-flash

And now we are ready to *flash*

    sudo ubuntu-device-flash core \
        --platform am335x-boneblack \
        --size 4 \
        --install mydevice_sergiusens_1.0_all.snap
        --output bbb_custom.img

If everything went well, the boot partiton will hold your custom dtb instead of
the default one, specifying `--platform` is required for this.

Please note that some of these things described here are subject to change.
