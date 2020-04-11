---
layout: post
title: Home automation ecosystem
date: 2018-01-04
categories:
tags:
comments: true
---

Automating your home is a fun task, if for nothing else, than for the learning path you will cross during this process. Learning curve can be quite steep for someone that wasn't exposed to overall Home IoT systems, so in this post, I'll try to explain how your home ecosystem could look like, what are the layers, how they interact with each other, what are the software (and hardware) option for each layer and try to offer some pointers for further investigation.

# Overview
It all started few years ago when Chinese company under an Espressif brand released their dirty-cheap Wifi-enabled micro-controller called ESP8266. Since you are here, I assume you know what it is, so I won't spend time explaining how great and powerful it is.

Next crucial moment in history was porting of Arduino development framework to Espressif platform, opening doors to vast range of libraries, sensors and even addon boards originally made for Arduino.

Being Wifi-enabled and having (almost) full IPv4 TCP stack, ESP8266 was a nice starting point for many dreamers to build a grid of sensors to turn their home into "Smart home" without opting for one of the closed source solutions offered by many mainstream companies, including Belkin, NetGear, Ikea and alike.

ESP8266 itself was not powerful enough to be the only piece of the puzzle and typical home would include few other pieces, namely a Wifi-Mesh, MQTT controller, home automation software, monitoring platform, build and OTA server and many other things.

This post is not a tutorial on how to set any of the options up. I might write that sometime in the future.

# Architecture

Overall ecosystem architecture includes few layers:

* Automation logic
* Metric collection system
* Management system
* Message Bus
* Network
* Sensors
* Actuators
* Firmware
* Board

## IoT Hardware
Hardware includes all nice little things we usually get from Ebay or Aliexpress/DealExtreme, such as fabricated modules, individual boards, sensors or actuators... Role of the hardware is to sense the physical world (using sensors), inform automation system about it and take orders on how to interact with the physical world (through actuators). Hardware needs to run firmware and can have /some/ automation logic built-in to make local decisions, but those are usually very simple (like switch-relay link, or timers).

### Board
Hardware that packs not only ESP8266 chip (which I'm focusing here) but also some Flash, voltage regulators and GPIO breakouts, possibly with some actuators and sensors. Most famous one is Itead Sonoff family of boards, but there are others that package number of useful things in a nice plastic fire-retardant package.

Many pre-build boards exist on the market and are very affordable: I already mentioned Itead Sonoff, but there's also Electrodragon IOT Relay, Arilux/MagicHome LED controllers, Huancanxing, Xenon and other Chinese modules. Aliexpress.com or Ebay are your best friends.

Braver ones might want to explore naked boards, such as Wemos D1 mini, and pair them with one of the 100s sensors / actuators out there. For any useful packaging, you should extend your project to include housing (note: 3D printed ABS or PLA are not fire retardant plastic, so think twice!)

### Firmware
Technically, firmware is more a software than a hardware, but in this case, it's tightly coupled with hardware, hence in the same category. Many fabricated modules (like mentioned Sonoff) ship with their own firmware, but you are here because you are not happy with it and want more. Luckily, a lot of good OpenSource alternatives exist and I'll cover some of them in future posts. Firmware capability dictates what your board can do - what sensors and actuators can it drive, how can it talk to rest of the system and how can you manage it.

Both sensors and actuators can use simple communication channels (pushbutton sends a "1" (high) signal on GPIO2, or setting GPIO9 to "High" will turn specific relay On), but they can also use more complex means of communicating such as serial bus, 1-Wire, I2C, SPI buses and protocols, or simpler PWM or Pulse Counting, IR encoding, or perhaps 433Mhz RF, nRF24L or ZigBee networks to reach standalone sensors/or actuators. Some of these are implemented in hardware itself, but most are software based solutions and depend on firmware capabilities.

To interface users and other parts of the ecosystem, firmware should support protocols like MQTT (surprisingly, that's not default for most factory provided firmwares), TLS (think security) and have some administrative interface that you can use to configure and monitor your board.

Many OpenSource options exist, Sonoff-Tasmota, ESPurna and ESPeasy being the most popular ones. Choice of firmware, together with choice of automation system will determine how easy can you do a lot of things around your house, so I'm preparing a comparison post that I'll publish rather soon.

### Sensors
Sensor is any hardware that turns physical-world events and metrics into something computers can understand. Switches and pushbuttons are the simplest sensors, but list can include temperature and humidity meters; laser and microwave range meters; gas, particle, liquid, current, presence meters and counters, and many others...

Some boards include number of preinstalled sensors (for example, 2 buttons already wired to GPIO ports) and some include headers for you to connect compatible sensors to predefined GPIO ports. In a lot of cases, original board manufacturer didn't include the option of user-connected sensors, but you'll find ways to still do so by using soldering skills and a bit of patience.

### Actuators
In contrast to sensors, Actuators are any hardware that can interact with physical world by changing some of it's properties.

Relays and Solid State Relays (for turning AC and/or DC on and off) and Triacs and Transistors (for dimming AC or DC loads like lights and AC fans) are simple ones, but list goes on to DC motor/fan drivers, liquid and gas valves, garage doors and many other fun stuff.

## Network
Logically, all devices and components of the system should somehow be connected. ESP8266 offers built-in WiFi, so naturally, you need Wifi network spanning throughout your house, and in some cases, to a wider area around your house (ie. when you are automating your garden irrigation). While many users will have a single Wifi Access Point with multiple antennas, others might decide to have multiple independent APs or Wifi Mesh covering whole area.

Security is also a big concern when it comes to networking, as many decide to mix IoT and "regular" devices on a single network, or even worst, open up some IoT to Internet access. I'll explore some of the best-practices in further posts.

## Message bus
High-speed, high-capacity system for passing messages between the devices and automation software. In IoT industry, MQTT (as standard and protocol) is chosen as preferred option, but in home environments, we also see some other technologies, like REST, WebSockets or even CLI.

There are number of hosted MQTT servers out there and some of them are free of charge, but security, latency and data confidentiality are probably reason why you might want to have your own MQTT server.

Popular choice is Mosquitto, mainly due it's small footprint and ease of installation, but alternatives like EMQ and VerneMQ also exist.

## Automation logic
System, framework or a simple "if <condition> than <action>" logic that implements 3rd Newton law in your house (action and reaction - connects the sensors input to actuators output).

Logic can be as simple as "when I flip a switch on device, turn on a relay on same device" (and will probably be implemented on the device itself), but can be as complex as "turn on my bathroom fan and open the window if difference between bathroom and living room temperature is higher than 15 degrees, but only if humidity is higher than 70% or if it's summer" which will collect data from more than one sensor and implement action on more than one actuator (this is, btw, real life example ;)

Popular choices are Home-Assistant, Domoticz, OpenHAB, MisterHouse, OpenMotics, Smarthomatic, Calaos, but people usually start with very simple one: Node-RED. It's hardly even comparable to the likes of Domoticz or Home-Assistant, but offers very intuitive and quick configuration perfect for early experiments.

## Metric collection system
System for collecting all data periodically exposed by all sensors around your home, and keeping them in a database (typically time-series database) so you can display nice graphs and get nice reports about your home.

For more serious usage, you can use long-term data to derive patterns and trends, which you can then use for your automation (for example, turn on heating 15 minutes before you would usually come home, and let system figure out when did you come home in the last 4 weeks).

These systems are usually complex on their own, consisting of collector, database and graphing frontend, but that's topic for another post.

In some case, like Home-Assistant or OpenHAB, metric collection functionality is bundled together with automation logic, so you only need to add database backend to the picture. Popular choices include InfluxDB, Graphite, Prometheus, but can be as simple as plain-old RRD.

## Management systems
Since your house is smart now, you need to make sure all systems are working, so you should setup a monitoring system to at least alert you to failures so you know what you need to go fix.

Also, a lot of software gets updated quite regularly, and you might want to have a centralized build or OTA serving platform for your devices.

Prometheus, CollectD, Cacti or Zabbix are some of the choices for monitoring system, while PlatformIO, combined with a bit of Ansible, can be a good choice for build / OTA system.

# Summary
Most people starting with IoT will get few boards, flash custom precompiled firmware to them, connect them to public MQTT servers and use one of the hosted automation systems. This gives you quick results and is fine for a beginning, but very soon you might feel that this is not good enough and that you need to have your own setup to experiment.

This is the time you will probably already have 5-6 IoT toys and you'll invest in Raspberry Pi (if you already don't have one), stage your own Linux with local copy of Mosquitto and Node-RED, Domoticz or Home-Assistant.

Once you decide to actual put something in "production" (daily use), or expanding your home installation to include even more device that you want to rely on, you'll start thinking about aspects like High-Availability, Security and Operability. This is the time you will definitely need more than one host running Linux (for load sharing / high-availability), but you might find Raspberry Pi not up to this task, so you might want to invest in one of the NUCs to support your home.
