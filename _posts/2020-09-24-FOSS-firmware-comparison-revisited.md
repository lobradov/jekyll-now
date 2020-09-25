---
layout: post
title: 2020 update on Home automation firmware projects
date: 2020-09-24
categories:
tags:
comments: true
---
Quite some time ago, I wrote few articles on [FOSS frameworks for Home automation](/FOSS-Firmware-comparison-overview). Life took me to other directions and I didn't had time to properly followup with latest changes nor write about it. Fast forward to 2 months ago, I finally got some time and restarted looking at what's new. This article tries to capture my findings and updates. 



# State of projects in 2020

Fresh look at the FOSS landscape in this area shows few important and relevant projects: 
- **Tasmota** - encumbent leader with biggest install base. A lot of good changes since I last wrote about it.
- **ESPurna** - Very clean firmware framework, still being strong even after Xose moved on to other projects. 
- **ESPEasy** - Not my favorite, but apparently having a good userbase and healty development. 
- **ESPHome** - "New" and very promising framework in the arena. "New" compared to last time I wrote review, but already almost 2 years and very active. Also, a platform that is most aligned with my opinion on how [Home automation ecosystem](/Home-Automation-Ecosystem) should look like. 

# Overview

## Feature comparison

|                   | Tasmota | ESPurna | ESPEasy | ESPHome | 
| ------------------| ------- | ------- | ------- | ------- | 
| Platform support  | ESP8266, ESP32 | ESP8266, ESP32 | ESP8266, ESP32 | ESP8266, ESP32 | 
| Major protocol support | MQTT, Telnet, HTTP/S, **KNX** | MQTT, Rest over HTTP/S, Telnet | MQTT, HTTP/S, **LoRA/TTN** | MQTT, Rest over TTP/S, **HomeAssistant Native API** | 
| Device Web UI     | Meh |  Very nice  | Meh | Just OK out of the box, can be very customized  | 
| Central device management | 3rd party Home Assistant plugin | None | None | Standalone web manager (docker), Home-Assistant device manager plugin |
| Device support approach   | per-device image, configuration options for similar devices | per-device image | per-device image, configuration options | complete build system |


Notes: 
- Per-device image usually means that a lot of features are pre-compiled and included in the image made for specific device. Sometimes, this approach might create images too big to enable single-step OTA, and generally, produce "dead-code" that will not be executed. 
- EPSHome does not feature own UI but a "Web Server" component with space (or links) for HTML/CSS/JS that can drive the device APIs. 

## Project health and community

Future of projects depend on the community that are developing it. Here are some (dynamic) metrics to help you get a picture of where they stand. Come back some other time to see refreshed, up-to-date data.

|                   | Tasmota | ESPurna | ESPEasy | ESPHome | Notes |
| ------------------| :-----: | :-----: | :-----: | :-----: | :---: |
| Total Downloads   | [![Downloads](https://img.shields.io/github/downloads/arendst/Tasmota/total?label=%20)](https://github.com/arendst/Tasmota/releases) | [![Downloads](https://img.shields.io/github/downloads/xoseperez/espurna/total?label=%20)](https://github.com/xoseperez/espurna/releases) | [![Downloads](https://img.shields.io/github/downloads/letscontrolit/ESPEasy/total?label=%20)](https://github.com/letscontrolit/ESPEasy/releases) | [![Downloads](https://img.shields.io/badge/not-applicable-lightgrey)](https://github.com/esphome/esphome/releases) | ESPHome is git cloned rather than downloaded in a classical sense. There's no statistics on cloning :/ | 
| Downloads (latest release) | ![](https://img.shields.io/github/downloads/arendst/Tasmota/latest/total?label=) | ![](https://img.shields.io/github/downloads/xoseperez/espurna/latest/total?label=) | ![](https://img.shields.io/badge/not-applicable-lightgrey) | ![](https://img.shields.io/badge/not-applicable-lightgrey) | ESPEasy are running night builds and are publishing releases as such.
| Date of last release | ![](https://img.shields.io/github/release-date/arendst/Tasmota?label=) |![](https://img.shields.io/github/release-date/xoseperez/espurna?label=) | ![](https://img.shields.io/badge/not-applicable-lightgrey) |![](https://img.shields.io/github/release-date/esphome/esphome?label=) |
| Release | ![](https://img.shields.io/github/v/release/arendst/Tasmota?label=) | ![](https://img.shields.io/github/v/release/xoseperez/espurna?label=) |![](https://img.shields.io/github/release/letscontrolit/ESPEasy/all?label=) |![](https://img.shields.io/github/v/release/esphome/esphome?label=) |
| Code size         | ![](https://img.shields.io/github/languages/code-size/arendst/Tasmota?label=%20) | ![](https://img.shields.io/github/languages/code-size/xoseperez/espurna?label=%20) | ![](https://img.shields.io/github/languages/code-size/letscontrolit/ESPEasy?label=) | ![](https://img.shields.io/github/languages/code-size/esphome/esphome?label=%20) 
| Commits per month | ![](https://img.shields.io/github/commit-activity/m/arendst/Tasmota?label=) | ![](https://img.shields.io/github/commit-activity/m/xoseperez/espurna?label=%20) |![](https://img.shields.io/github/commit-activity/m/letscontrolit/ESPEasy?label=) |![](https://img.shields.io/github/commit-activity/m/esphome/esphome?label=%20) |
| Contributors      | ![](https://img.shields.io/github/contributors/arendst/Tasmota?label=) | ![](https://img.shields.io/github/contributors/xoseperez/espurna?label=) | ![](https://img.shields.io/github/contributors/letscontrolit/ESPEasy?label=) | ![](https://img.shields.io/github/contributors/esphome/esphome?label=) | 
| Latest commit     | ![](https://img.shields.io/github/last-commit/arendst/Tasmota?label=) | ![](https://img.shields.io/github/last-commit/xoseperez/espurna?label=) | ![](https://img.shields.io/github/last-commit/letscontrolit/ESPEasy?label=) | ![](https://img.shields.io/github/last-commit/esphome/esphome?label=) | 
| Development language | ![](https://img.shields.io/github/languages/top/arendst/Tasmota) | ![](https://img.shields.io/github/languages/top/xoseperez/espurna) |![](https://img.shields.io/github/languages/top/letscontrolit/ESPEasy) |![](https://img.shields.io/github/languages/top/esphome/esphome) | [^espurna-language] 

[^espurna-language]: ESPurna is a project written in C, but features a lot of HTML files that show up in statistics 

# ESPhome 

ESPHome is impressive old-new take on the home automation. 
Instead of creating a code, compiling it throught the device configuration matrix and publishing per-device firmware, Otto Winter decided to create something new: he created a library of features and a code-generating configuration parser coupled with a build system that produces (and uploads) exactly the image your device nededs.Sounds complicated? It actually is not. 
Input to ESPhome is a yaml file that describes your device. Since ESPhome is written for Home Assistant, yaml format is similar to the one of Home Assistant, so you can find sections like: 
```yaml 
esphome:
  name: my_device
  platform: ESP8266
  board: d1_mini

  wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  fast_connect: true
```
When you run ESPHome, it will take the yaml, convert it into C/C++ code, compile the code and optionally upload it to the device. 
To do that, Otto and the team created a complete build system that can run either directly from git repo (it's a python script after all), or packaged in a Docker container (multi-arch!) or directly from Home Assistant. BUild process is fast and straitforward and built images contain only the code you asked for, nothing else, yealding very small firmware images for typical applications. 
Downside of this approach is everything you have in a device needs to be described and that, out of the box, you don't get anything preconfigured. Luckly, there are repositories of device samples that you can use and modify to suit your needs. 


# Personal take 

I'm very impressed by ESPHome, even though statistics are not showing its full potential and glory. It has a bit of a learning curve, especially if you are expecting to see prebuilt images for common devices, but it's ideal for anyone that wants to be in full control over their devices or has made a device on their own. 
I was so impressed that I became a project backer on parteon (and invite you to do the same) and will definitelly be playing with it more, so expect more writeup on this topic.

# Footnotes