---
type: docs
title: Introduction
linkTitle: "Home"
weight: 1
cascade:
# cf: https://www.docsy.dev/docs/adding-content/content/#alternative-site-structure
  - type: "docs"
    no_list: true
    #_target:
    #path: "/**"
---

HALPI is a pre-built, fully functional boat computer running a custom OpenPlotter 4 installation with pre-configured NMEA 2000 and other settings. It includes pre-configured Signal K server, OpenCPN and other essential open navigation software. Additional packages such as AvNav, InfluxDB and Grafana can be easily installed. HALPI can be extended using a number of Raspberry Pi HATs and other accessories.

{{< imgrel "halpi-photo.jpg" "60%" >}}
HALPI image
{{< /imgrel >}}

## HALPI-CM4 Key Features

HALPI-CM4 is based on the Raspberry Pi Compute Module 4 (CM4) with up to 8 GB of RAM.
HALPI computers include a fast solid-state drive (SSD) for storage.
SSDs are both much faster and more reliable than Micro SD cards.

HALPI includes the SH-RPi power management board which provides safe and transparent power-off functionality as well as a real-time clock (RTC) for accurate timekeeping without an Internet connection.
The computer can be powered via the NMEA 2000 network and includes a built-in NMEA 2000 interface.
It is guaranteed to not consume more than 0.8 A of current at 12 V.
Alternatively, it ca be powered through a dedicated power connector, providing full isolation from the NMEA 2000 network.
In either case, HALPI will shut itself down when the pwoer is cut, allowing it to be turned on or off using a standard electrical panel switch.

HALPI-CM4 comes in two different enclosure configurations: HALPI-M-CM4 and HALPI-S-CM4.
The medium size enclosure measures 170x140x100 mm and provides an IP65 ingress protection rating and includes pre-drilled holes and cable glands for additional connectors.
It provides plenty of space for additional HATs and accessories.
The small size enclosure measures 158x90x60 mm and provides the same IP65 rating.
It is more compact and suitable for installations where space is limited and fewer additional accessories are needed.

## Getting the Hardware

You can purchase HALPI computers from [Hat Labs Oy](https://shop.hatlabs.fi).


## Community and Support

Hat Labs provides support for the HALPI hardware at [info@hatlabs.fi](mailto:info@hatlabs.fi).

For software related questions, you can access the awesome open marine community over a number of forums and chat channels:

- [Hat Labs discussion board](https://github.com/hatlabs/discussions/discussions)
- [Signal K Discord (See invite link under "Community & Support")](https://signalk.org/)
- [OpenMarine Forum](https://forum.openmarine.net/)
