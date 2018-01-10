---
layout: post
title: Tasmota vs ESPurna vs ESPEasy - <br> overview
date: 2018-01-08
categories:
tags:
---

You are here because you are already aboard the Home IoT bandwagon, so I don't need to explain how interesting, exciting and fun it is, but are also here because you are not happy with the stock firmware Chinese vendors are providing for many of the ESP8266 based devices.
Luckily, community took this issue in their hands, and over the course of year and a half, we saw 3 amazing OpenSource projects that are addressing this (and many other) issues.
In the next few blog posts, I'll try to compare 3 most popular OpenSource firmware options from multiple angles, and hopefully help you decide which one to go with. This post is an overview and introduction into different options, with a very high-level comparison between them.


# Firmware overview


## [Sonoff-Tasmota](https://github.com/arendst/Sonoff-Tasmota)

Sonoff-Tasmota, standing for **T**heo-**A**rends-**S**onoff-**M**QTT-**OTA**, came as an evolution of previous tinkering projects by Theo Arends. Officially, project started in January 2017, but it inherited a lot of assets from previous two incarnations, so we can say that project actually started as early as January 2016.

As name suggests, it was originally built to replace stock firmware for early Iteads Sonoff devices, but it grew to a project that supports many more ESP8266 based boards with a lot of sensors not originally found on Sonoff products.

Project is updated almost daily, exclusively by Theo himself, reworking some of the contributions from the developers community.

Community is very vivid and has a healthy mix of developers and "regular" uses that make sure firmware is generally usable. This community is producing a lot of materials, including a lot of Youtube tutorials and very nice Wiki pages, contributing to adoption and success of Tasmota project.

## [ESPurna](https://bitbucket.org/xoseperez/espurna)

Very talented Xose Perez started ESPurna (play on the Catalan word for "spark") in May 2016, as a project to provide a device-agnostic firmware for ESP8266 boards. He apparently started with Wemos D1 Mini board (being very generic) and added support for many other fabricated boards, including Sonoff and others.

Xose is clearly an experienced software developer and a software architect, as project is very well organized and very well architected, but at the same time, is missing wider social adoption, despite very informative blog he runs.

Project is updated few times a day, exclusively by Xose, based on the discussions or minor contributions by community.

Community is smaller than the one of Tasmota and is mainly oriented towards developers skilled enough to match Xose's knowledge and skillset. While there are few Espurna related videos on Youtube, they are far from coverage Tasmota got.

## [ESPEasy](https://www.letscontrolit.com/wiki/index.php/ESPEasy)

Technically, ESPEasy is the oldest alternative firmware out there. It started in December 2015 by a Dutch team called "Lets Control It", as their experiments in IoT and presumably, as part of their new company offering. They started with generic ESP8266 platform and added documentation on how it can be used for fabricated boards, such as Sonoff, which is clearly a hardware of choice for many Home IoT projects. This approach makes it very flexible and able to support any combination of sensors / actuators, but requires a bit more configuration to get started.

Project is updated many times a day, mostly by few core developers from Lets Control It, but they also merge code from other contributors, which is quite nice and in the spirit of true OpenSource project.

Community size is comparable to the one of Tasmota, with a bit better balance between developers and "regular" users.

# Common functionality

All 3 firmware options share some of the common properties:

* They are **Open Source** and built using **Arduino framework**, and offer **pre-compiled binaries**.
* They support almost all **Itead Sonoff** and quite a few other boards and modules, including all 1M Flash devices. Of course, generic boards like NodeMCU or Wemos D1 are supported as well.
* All have a wide support for a lot of **additional sensors and actuators**, including popular choices of I2C and 1-Wire sensors and power monitors. ESPEasy takes it even step further with a lot of less usual stuff you would not consider as typical part of Home automation.
* All support various **LED controllers** with full LED color rendering, dimming, transitions and some effects.
* All support various **WiFi connection modes**, including AP (hotspot) or STA (client) mode, with multiple SSID configuration and Wifi Network scanning.
* They support **MQTT**, both for sensors/actuators, as well as for own configuration. Some even offer specific topic layout tailored for easier integration with popular automation logic software, such as Home Assistant or Domoticz.
* All support some protocols specific to home automation software, such as **Domoticz HTTP**, **OpenHAB HTTP** and some others.
* They all support integration into **Google Assistant** and **Amazon Alexa**.
* Of course, all support **Over-the-Air update (OTA)** and configuration **backup and restore**.
* All firmware options have some **local automation** embedded (button/switch to relay mapping), with some taking one or two steps further in adding more capabilities (read below for more details).
* They all support **logging** over some interfaces, such as Serial, Syslog or HTTP, with configurable log levels.
* All support time synchronization via **NTP**

## Common areas for improvement

All firmware options suffer from the same common set of issues and potential areas for improvement:

* **Secure communication / TLS** - Neither firmware has proper support for TLS on all interfaces, making overall security very hard to implement. Tasmota and ESPurna began supporting TLS for MQTT, but that addresses only part of the problem, and leaves Web UI and various other interfaces unencrypted and unauthenticated.
* **Documentation** - being OpenSource projects, neither firmware has a well structured, properly designed and implemented set of documentation that would really make it easy for average user to get to quick results. While some (Tasmota) are better than the others, it's still a long way to go before average Joe would be able to download installation package and get his Sonoff up and running in 2 minutes. Maybe that's currently not the focus, but I find more than 50% of questions in forums repeating over the same 5% of beginners issues that could be solved by proper documentation.
* **IPv6** - Neither board supports IPv6 and relies on IPv4. While this might be less relevant in home environments (where you can freely choose your own Private IP layout), it's still taking away part of fun for planning and "doing things right". I understand IPv6 is more of a Arduino/ESP8266 framework issue than a individual firmware issue, but it's still an issue :)

# Notable functional differences

## Sonoff-Tasmota

### Good

* **Web CLI** - Tasmota features console that can be used to debug and configure some aspects of the firmware which are not exposed over Web UI. Console is still accessed using browser and in some cases, is mandatory to set the device properly, which is also a negative point for this firmware.
* **IR send and IR receive** - Tasmota supports both raw and processed IR send and receive codes, embedding support for major IR protocols. It also has high-level commands for Air conditioning control (utilizing IR send specifically for HVAC devices).
* **WS2812 LED string** - with special "clock" mode for circular WS2812 strips.
* **433Mhz RF** - receiving / learning / sending 433Mhz RF codes.
* **mDNS discovery** - can discover MQTT server using mDNS protocol and connect to it.
* **Multiple language support** - Sonoff-Tasmota includes I18N framework, and currently offers English, Deutsch, Dutch, Polish and Italian translations. I saw a PR with Simplified Chinese, and I'm working on Serbian translation as well.
* **One-size fits all precompiled binary** - Theo is really providing 2 precompiled binary images (excluding variants for language): minimal image (used only for initial bootstrap) and one-size-fits-all image that includes support for all boards and all sensors / actuators.  This is great for experimenting, but is not necessary once you know what your board configuration is and can also lead you to trouble (configuring wrong board type might require you to reconnect your board to programmer, erase flash and re-flash Tasmota).

### Can be improved

(disclaimer - I have most experience with Tasmota, hence this detailed list)

* **Inconsistent MQTT message policy** - Tasmota sometimes has inconsistent MQTT messages, making it hard to integrate into some home automation systems. Still, once you dig into documentation / forums and once you find out what works, you'll be mostly fine.
* **Dependency on commands** - some functionality is impossible to set using WebUI and set options are not visible in debug messages. Reading docs, experimenting and knowing what you are doing helps.
* **"All code needs to fit 1M boards" policy** - To ensure compatibility with smallest Flash sizes, Theo is obsessed with size and insists on including only features that all together fit 500K (half of 1M, to allow OTA). While everyone can recompile images to use different board layout, some features were not included, or were degraded in functionality to size concern as "one-size-fits-all" image doesn't fit magical 500K. Seems, with v2.4.0 of Arduino/ESP8266 framework, this will have to change, so dual-step-OTA process, introduced in June 2017 as suggested option and a best practice, will become mandatory.
* **Basic push-button support** - buttons connected to Tasmota can either send press or long-press signal over MQTT, but not double-press, triple-press and similar gimmicks. Double-press is quite useful feature and it's shame it's not supported.
* **MQTT failover** - Tasmota doesn't have an option to include secondary MQTT broker configuration, that would be used in case primary is out. This is partially mitigated by mDNS selection of MQTT brokers (Tasmota can automatically connect to a host advertising itself as ._mqtt endpoint over mDNS) but I'm personally not using this option due to security concerns and would rather have a configurable list of MQTT brokers.

## ESPurna

### Good

* **Home-Assistant auto-discovery** - ESPurna can publish data in a format discoverable by Home-Assistant, making integration of various switches, buttons, relays and lights automatic.
* **Telnet support** - similar to Tasmota, but simpler, ESPurna offers unencrypted telnet interface that is perfect for debugging. For security reasons, it must be explicitly enabled in Web UI before usage.
* **433Mhz RF** - receiving / learning / sending 433Mhz RF codes.
* **mDNS, NetBIOS and LLMNR and SSDP advertising and discovery** - mDNS advertising being especially interesting, rest being there "because we can" ;)
* **Direct InfluxDB integration** - can export metrics directly to InfluxDB, without a need for intermediate collector.
* **Tailored binaries and integration with NoFUSS update framework** - Xose put a lot of effort into building a matrix of most used boards / sensors / features, and is offering individual images specifically tailored for your need. NoFUSS update framework promises to take care of subsequent upgrades by automatically keeping track of used firmware on specific board, but I haven't tested it in practice to be able to say how it actually works.

### Can be improved

* **Board type and hardware is set during compilation time** - unlike other options, ESPurna requires you to either download right image for your board, or chose a board during compilation time, which will hardcode most of the hardware-related settings. This is not normally an issue once you configure everything, but can be a rough start, especially if you don't know what you want / need.
* **Documentation and user forums** - it's not that ESPurna is not documented, but it's just that overall quality of documentation is not always up to average user level, making venture into ESPurna a bit steep learning curve. Similar could be said about user forums.
* **MQTT failover** - Similarly to Tasmota, ESPurna doesn't allow you to configure more than one MQTT broker, so you can't have failover scenarios. EPSurna doesn't discover MQTT brokers, so the only way to get around this issue is to design your broker implementation to be High-Available. This is, btw, a proper way of dealing with the issue (each service should make sure itself is highly available), but firmware support for client-side failover handling would be appreciated as quick workaround.

## ESPEasy

### Good

* **Node discovery and internode communication** - one unit can discover other units and enumerate them in a Web UI and can also send HTTP and UDP commands to them. In theory, this would allow you to run a network of boards without MQTT broker.
* **Local rules** - script your own local application logic on the controller itself, without the need for MQTT broker and additional automation software. Very powerful, especially if combined with internode communication, but I honestly didn't get to play with it too much, so can't tell how usable really is.
* **Wide sensor framework** - allowing "raw" I2C sensor data dump to MQTT, which in turns supports Gyroscopes, RFID sensors, buzzers and external displays, including MP3 player.
* **WS2812 LED string** - with special "clock" mode for circular WS2812 strips.
* **Virtual IO support** -  apart from directly connected relays and buttons, ESPeasy can support "virtual relays" and "virtual switches" connected via one of the IO multiplexers, allowing addressing of up to 128 switches/relays in a consistent manner.
* **I2C scan from Web UI** - If you have a bus of unknown I2C devices, you can issue a scan right from the WebUI and get a list of all the devices, together with their properties. Pretty nice :)

### Can be improved

* **No preconfigured boards** - with ESPEasy, you can configure everything, and you need to configure everything, so you can't choose one of the existing boards, but have to setup everything from scratch. This is not only delaying your initial results, but can potentially be dangerous, as ESPEasy allows you to reconfigure GPIOs that would normally be used by Flash, rending board useless until you erase and reflash (similar to Tasmota board misconfiguration). Not a big deal once you know what you are doing, but quite frustrating if you need to start fast.
* **Powerful but confusing HTTP API reconfiguration** - while ESPEasy allows you to configure great deal of HTTP API, it's a bit confusing to understand what/where/how. Again, once you get it configured, it just works, but learning curve might be a bit steeper.
* **UI is for configuration only** - funny enough, but you can't trigger a state change on a relay connect to the board via ESPEasy. You'll have to use HTTP or MQTT API, or at least WebUI -> Tools-> Command->manual typing commands. While there is some logic in this decision, I found it very surprising.  

# Summary

Community has build at least 3 good alternatives to vendor-provided firmware and each will satisfy most of the basic use-cases, such as controlling relays or signaling temperature reading using MQTT. For more, you'd have to look at your use-case and see which of the firmware is more suited.

(disclaimer: I mostly use Tasmota and provide minor contributions to it, hence my stronger opinion about it).

I found **Tasmota** to be "good enough" for everyday use (almost all my home devices run Tasmota), though there are some things I'd love to see changed. Tasmota allows easy start and quick results, has powerful set of features and good support for different sensors, but also comes with some weird stuff that you just have to get used to. Apart from the standard method of flashing, community around Tasmota (including yours truly) offers a bit weird OTA method of flashing initial Tasmota image over the stock Sonoff firmware, without soldering or even opening some of the supported devices.

**ESPurna** is very structured and offers clean and precise interface, but it didn't yet "clicked" between us, so I didn't get to install it in any of the "production" devices I own, mainly because I started with Tasmota and would like to minimize number of variants I have in my house.

Still, it seems it has better internal implementation of MQTT client and HTTP server (true non-blocking), so I'm currently testing it to see it they would be good to replace network of Tasmotas and maybe jump onto ESPurna development bandwagon (which is a motivation for writing this post). ESPurna might be lacking some of the fancy features Tasmota has, so your milage may vary.

I personally found **ESPEasy** too unstructured and too loose for mass production (aka daily use), but on the other hand, I love it when it comes to experiments with new hardware and new stuff, exactly because of the freedom and configurability it gives you. It has very open but very powerful run-time configuration capabilities and is most advanced when it comes to local decision making, potentially reducing the need for MQTT broker and external automation logic. For me, that just doesn't sound right, but I know at least handful of people that would strongly disagree with me on this.

# What's next?

In theory, all off the existing firmware options offer quite a good set of operability features, like Web UI, OTA, backup and restore, but I will dig into the usability in the next post, to explore how easy it is to really use this software on a daily basis and to rely on it for your home daily operations. I'll try to look at the things from pure end-user perspective, without going too much into implementation and development details.

Also, I assume some of you are fellow tinkers and programmers and would like to use some of these frameworks as base for own code, so I'm preparing another post about same 3 firmware, just from developers perspective, where I'll try to compare actual coding style, cleanness, community, and tooling used by these projects.
