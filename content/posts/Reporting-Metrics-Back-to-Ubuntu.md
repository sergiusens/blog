---
title: "Reporting Metrics Back to Ubuntu"
date: 2018-08-08T20:22:04-03:00
draft: false
categories: [ubuntu]
tags: [hardware,bangho]
---

# A short lived ride
After some time on Kubuntu on [this new laptop](../new-laptop), I just re-discovered that I did not want to live in the Plasma world anymore. While I do value all the work the team behind it does, the user interface is just not for me as it feels rather busy to my liking.

In that aforementioned post I wrote about running the [Ubuntu Report Tool](https://github.com/ubuntu/ubuntu-report) on this system, it is not part of the Kubuntu _install_ or _first boot experience_ but you can install it by running `apt install ubuntu-report` followed by running `ubuntu-report` to actually create the report and if you want, send it too.

# Back to Ubuntu
After the announcement of the Ubuntu 18.04.1 release, which interims on top of the [Ubuntu 18.04 LTS](https://blog.ubuntu.com/2018/04/27/breeze-through-ubuntu-desktop-18-04-lts-bionic-beaver) release, I went ahead and got it running on this laptop. During the _first boot experience_ I went through the steps to eventually _report my system setup_.

Here's the output from running this tool right now for this laptop:

```
{
  "Version": "18.04",
  "OEM": {
    "Vendor": "BANGHO",
    "Product": "N13xWU"
  },
  "BIOS": {
    "Vendor": "American Megatrends Inc.",
    "Version": "1.05.9RPC"
  },
  "CPU": {
    "OpMode": "32-bit, 64-bit",
    "CPUs": "8",
    "Threads": "2",
    "Cores": "4",
    "Sockets": "1",
    "Vendor": "GenuineIntel",
    "Family": "6",
    "Model": "142",
    "Stepping": "10",
    "Name": "Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz",
    "Virtualization": "VT-x"
  },
  "Arch": "amd64",
  "GPU": [
    {
      "Vendor": "8086",
      "Model": "5917"
    }
  ],
  "RAM": 8,
  "Partitions": [
    242.8,
    0.7,
    0.5
  ],
  "Screens": [
    {
      "Size": "294mmx165mm",
      "Resolution": "1920x1080",
      "Frequency": "59.93"
    }
  ],
  "Autologin": true,
  "LivePatch": false,
  "Session": {
    "DE": "communitheme:ubuntu:GNOME",
    "Name": "ubuntu-communitheme-snap",
    "Type": "x11"
  },
  "Language": "es_AR",
  "Timezone": "America/Argentina/Cordoba",
  "Install": {
    "Media": "Ubuntu 18.04.1 LTS \"Bionic Beaver\" - Release amd64 (20180725)",
    "Type": "GTK",
    "OEM": false,
    "PartitionMethod": "use_crypto",
    "DownloadUpdates": true,
    "Language": "es",
    "Minimal": true,
    "RestrictedAddons": true,
    "Stages": {
      "0": "language",
      "6": "console_setup",
      "19": "console_setup",
      "66": "prepare",
      "115": "partman",
      "132": "partman",
      "247": "start_install",
      "280": "timezone",
      "283": "usersetup",
      "323": "user_done",
      "1068": "done"
    }
  }
}
```

As you can see, it does not have super fancy _out of this world_ specs, but it gets the job done for me. It is also, my _**only**_ machine. I do have others, but they just collect dust on some shelf around the house.