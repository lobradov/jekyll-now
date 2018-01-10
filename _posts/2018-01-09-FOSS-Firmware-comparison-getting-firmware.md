---
layout: post
title: Tasmota vs ESPurna vs ESPEasy - <br> getting the firmware
date: 2018-01-09
categories:
tags:
---

I'd like to share some of my experiences getting and flashing one of the precompiled firmware options and will also explore a process of building your own firmware to better suit your needs.

# Getting firmware for your board

## Precompiled binaries

As mentioned in previous post, all 3 options (Tasmota, ESPurna and ESPEasy) offer pre-compiled binaries, so you can just download the image you need and get going very fast.

**Tasmota**, offers 2 main variants of the firmware: minimal image (only good for initial flashing) and one-size-fits-all image (with all features turned on). Later is actually provided for every language variant, so there are 1+4 images provided, but logic stays the same.

Images are located [here](https://github.com/arendst/Sonoff-Tasmota/releases).

**ESPurna** offers 52 different pre-compiled images, to exactly match your need. Have Sonoff Basic module? Get `espurna-1.11.3-itead-sonoff-basic.bin`. Added DHT sensor to it? Get `espurna-1.11.3-itead-sonoff-basic-dht.bin`. You get the idea.

While this might be interesting in the beginning, it's also a bit of annoying as some combinations might not be available, so you are left with no choice but to compile your own.

Repository of images is available [here](https://bitbucket.org/xoseperez/espurna/downloads/?tab=downloads)

**ESPEasy** also comes precompiled, and is offering 3 everything-included images, made for different flash sizes your boards might have (they offer firmware for 1M, 2M and 4M flash boards, conveniently named 512, 1024 and 4096... why? because!). They also include upload tool for Windows in the archive, together with a source tree of ESPEasy, which makes is quite convenient for flashing.

Always fresh link to actual image archive is provided [here](https://www.letscontrolit.com/wiki/index.php/ESPEasy#Loading_firmware)

## Compiling your own firmware

If you didn't find what you like, or you would like to customize the image that you are going to load to your devices, you should consider compiling your own set of images.

Compiling your own firmware is not as hard as it seems, and, once you overcome the initial fear of it, will become your preferred method of getting new versions of the firmware.

All 3 firmware options prefer PlatformIO as development / build environment, and given it's super easy to set up on all platforms, I'm not even considering alternatives. You'd need PlatformIO, git client, a bit of patience and literally few minutes to get all setup to build firmware from source.

For all 3 firmware, process boils down to:
```console
$ git clone https://github.com/<project>.git
$ cd <project>
$ pio build
```

One of the rules I set for myself is not to have a dead-code compiled and uploaded to the devices, so I prefer images where I can choose exactly what I need. This way, images are small and upload fast, and I limit the interference of the code I don't intent to run. In my day job, this is very important aspect of software design and usability, so I'm applying it to my home automation network.

So, before you actually build your images, you might want to change some of the parameters that will govern what options get compiled in your firmware.

### Tasmota

Tasmota uses `user_config.h` and `user_config_override.h` to set a lot of options, including support for various modules, initial Wifi access parameters and similar. Most of the parameters are self explanatory and have comments on a side that would help you determine what you should change. If not sure, don't change defaults :)

Most important parameters are the ones starting with `USE_`, that determine if support for specific functionality should be compiled in the firmware. For example, if you would like to use have support for Domoticz, but don't care about PZEM004T power monitor, relevant part of your user_config.h should look like:

```
#define USE_DOMOTICZ
// #define USE_PZEM004T
```

Notice // in front of the line, which comments out use of specific modules or sensors.

Good practice is to use `user_config_override.h` to define your own configuration, but, since all parameters are defined as C preprocessor defines, you need to undefine before you redefine, as in:

```
#undef STA_SSID1
#define STA_SSID1 "my-wifi-ssid"
#undef STA_PASS1
#define STA_PASS1 "my-wifi-password"
#undef MQTT_BUTTON_RETAIN      
#define MQTT_BUTTON_RETAIN  1
```

Bad side of this practice is the fact that you have to create `user_config_override.h` for every different board/configuration you want to compile for, or would create one-size-fits-all image your self. For example if you'd like to have firmware for 5 devices without DHT support and one for 2 devices with, you'd have to maintain separate user_config_overrides.h and juggle with them between the builds, or simply build an image with DHT and live with it.

### ESPurna

ESPurna internally uses same C preprocessor defines to determine which boards / sensors to compile in, but Xose decided to externalize those parameters as build flags, rather than part of configuration files. This means that you can configure your board by supplying number of `-D<option>` in `platformio.ini`, rather than changing the C code.

You can check all available options by examining `code/espurna/config/arduino.h` file, and use some of the defines.

Truth be told, Xose left the option of defining your own `custom.h` and leaving it in the same directory, but both of us would discourage you to do so, when you can more easily create a new build target in PlatformIO.

For example, to compile Sonoff 4Ch with DHT module attached to one of its GPIOs but without Domoticz support, I would add following lines to `platformio.ini`:

```ini
[env:itead-sonoff-4CH-DHT-no-Domotics]
platform = ${common.platform}
framework = arduino
board = esp01_1m
board_flash_mode = dout
lib_deps = ${common.lib_deps}
lib_ignore = ${common.lib_ignore}
build_flags = ${common.build_flags_1m} -DITEAD_SONOFF_4CH -DDHT_SUPPORT=1 -DDOMOTICZ_SUPPORT=0
monitor_baud = 115200
```

Notice how I added my own name of the board (`itead-sonoff-4CH-DHT-no-Domotics`) and said I'll use 4CH board (`-DITEAD_SONOFF_4CH`), add DHT (`-DDHT_SUPPORT=1`) and disabled Domoticz (`-DDOMOTICZ_SUPPORT=0`). Feel free to customize your other options.

Once you have your boards defined, you can just go ahead and `pio build <board-name>` and your image will be ready.

### ESPEasy

As I mentioned in my previous post, almost all of ESPEasy configuration is runtime (rather than compile time) so there are not a lot of options you can se during compilation time. Build system offers more choices than you get with precompiled binaries, so now you can choose firmware for ESP8266 and ESP8285 chips, with various Flash sizes and with, or without included development plugins.

Build process will result in a zip archive similar to the one you can download, just with few more firmware options.

# Flashing

All 3 firmware option prefer serial upload of the images to boards, which requires you to have FTDI programmer module (and make sure you have real 3v3 version one!)

Images can be flashed using `esptool.py` or similar tools, as described in each of the firmware documentation pages, and, of course, through PlatformIO upload functionality:

Tasmota [Upload documentation](https://github.com/arendst/Sonoff-Tasmota/wiki/Upload)

ESPurna [Binaries documentation](https://bitbucket.org/xoseperez/espurna/wiki/Binaries.md)

ESPEasy [Connecting and flashing](https://www.letscontrolit.com/wiki/index.php/Basics:_Connecting_and_flashing_the_ESP8266)

Process is actually same for all 3 firmware options, so I suggest that you read all 3 instructions to understand the root principle behind uploading a firmware. After that, the only difference is a filename ;)

## Initial flash of Sonoff devices via OTA

By default, Itead Sonoff devices include custom firmware with custom OTA mechanism that will try to update Itead-supplied firmware to newer version. This OTA mechanism is not compatible with any of the OpenSource firmware (that all use Arduino OTA framework).

Few very smart people behind [SonOTA](https://github.com/mirko/SonOTA) and [Espressif2Arduino](https://github.com/khcnz/Espressif2Arduino) projects figured out how to intercept those requests and serve your firmware of choice instead of Itead one.

Process involves quite some juggling with differnet Wifi networks, intermediary firmware and has few steps, but still - works, so I recommend it to folks that don't want to open their devices or that don't have FTDI programmer. Note that not all Itead devices are supported.

Process for Tasmota (coauthored by yours truly) is available at [SonOTA-Espressif2Arduino-Tasmota-without-compiling](https://github.com/arendst/Sonoff-Tasmota/wiki/SonOTA---Espressif2Arduino---Tasmota-without-compiling)

Process is a bit weird due to default Tasmota firmware trying to connect to Theo's home Wifi (SSID: `indebuurt1`), but you can take a step back, and change the process to serve ESPeasy and ESPurna firmware. I might provide a writeup for that somewhere in the future.

# Summary

All 3 firmware are easy to get precompiled binaries and easy to compile one for yourself.

Game is the same for all 3 options, when it comes to pre-compiled binaries, with Tasmota and ESPEasy having all-included binaries that are less complicated to start with than ESPurnas matrix of choices.

However, for roll-your-own images, I personally set a rule that I will not include "dead code" on my devices, and would only compile and upload the code I actually will use. In this light, I prefer ESPurna way of configuring different boards, as it allows me to select and include only the code I actually need and have different configuration for different boards, without juggling with files. If you only have one type of boards, Tasmota offers same experience, but it become a bit complicated if you have multiple boards.

ESPEasy is not in this bucket, as you don't really have option not to include code you don't need but need to compile everything there is. It's still a good firmware and if you ignore my preference, you should be good with it.

Flashing process is same for all 3 firmware and is very well documented.

# What's next?
Now that you have one or the other firmware loaded, I wanted to walk you through a configuration process to get each of the firmware connected to your home Wifi, MQTT and configure simple sensor/actuator to be used by the board.
