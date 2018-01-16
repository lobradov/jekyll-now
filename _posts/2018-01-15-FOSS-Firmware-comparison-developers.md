---
layout: post
title: Tasmota vs ESPurna vs ESPEasy - <br> contributing and developing
date: 2018-01-15
categories:
tags:
comments: true
---

You have some coding skills and know at least basic C/C++? You want to add more features and improve the quality of the code? Found something cool and want to document it?
Read on to see how each of the 3 OpenSource firmware look "under the hood" and how does their development process and code look like.

# TL; DR

|                       | Tasmota       | ESPurna       | ESPEasy       |
| --------------------- | ------------- | ------------- | ------------- |
| Repository            | [github.com](https://github.com/arendst/Sonoff-Tasmota) | [bitbucket.org](https://bitbucket.org/xoseperez/espurna/) | [github.com](https://github.com/letscontrolit/ESPEasy) |
| Wiki / forum / blog   | [github.com Wiki](https://github.com/arendst/Sonoff-Tasmota/wiki) <br> [Google Forum](https://groups.google.com/forum/#!forum/sonoffusers) | [bitbucket.org](https://bitbucket.org/xoseperez/espurna/wiki/Home) | [letscontrolit Wiki](https://www.letscontrolit.com/wiki/index.php/ESPEasy/)<br> [letscontrolit forum](https://www.letscontrolit.com/forum/) |
| Issue tracker         | [github.com](https://github.com/arendst/Sonoff-Tasmota/issues) | [bitbucket.org](https://bitbucket.org/xoseperez/espurna/issues?status=new&status=open) | [github.org](https://github.com/letscontrolit/ESPEasy/issues) |
| Continuous integration | [TravisCI](https://travis-ci.org/arendst/Sonoff-Tasmota)      | ?            | [TravisCI](https://travis-ci.org/letscontrolit/ESPEasy)       |
| Languages             | C       | C++, Javascript | C    |
| Lines of code (without libraries)   | 17k            | 19k            | 29k         |
| Code quality (Codacy) | [![Codacy Badge](https://api.codacy.com/project/badge/Grade/4c73be2b79c54351838abf5aeddf4d99)](https://www.codacy.com/app/lobradov/Sonoff-Tasmota?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=lobradov/Sonoff-Tasmota&amp;utm_campaign=Badge_Grade) | [![Codacy Badge](https://api.codacy.com/project/badge/Grade/c7ff791c40ee4847a88f03c29101e88c)](https://www.codacy.com/app/lobradov/espurna?utm_source=lobradov@bitbucket.org&amp;utm_medium=referral&amp;utm_content=lobradov/espurna&amp;utm_campaign=Badge_Grade) | [![Codacy Badge](https://api.codacy.com/project/badge/Grade/7dffa2415f424437a2a226a73ce262fc)](https://www.codacy.com/app/lobradov/ESPEasy?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=lobradov/ESPEasy&amp;utm_campaign=Badge_Grade) |
| Code readability ([^1]) | 2/5       | 4/5           | 3/5           |
| Good                      | - Good enough tooling <br> - Open Wiki | - Clean, structured code <br> - Well architected solution <br> | - Good enough tooling <br> - Structured code <br> - Contribution policy |
| Can be improved           | - Contribution policy <br> - Complex code <br>  | - Incomplete tooling <br> - Closed wiki <br> - Contribution policy <br> - Partial design documents | - Core code complexity |


[^1]: My completely subjective score based on code organization, readability, comments and design. YMMV.

# Tooling

As developer, you know how tooling of a project is important. You might have your preference with code repo, Issue tracker, Design and Documentation wiki, Contribution process and everything else. Let's take a look at different tools each of the 3 projects use.

## Tasmota

Theo is hosting Tasmota code on [GitHub](https://github.com/arendst/Sonoff-Tasmota) and maintains 2 main branches:
- development - used for daily work
- master - "Stable" releases

Theo accepts Pull Requests to development branch, but is almost always reworking them and provides own commits with the same functionality. While this approach, compared to merging PRs, might result in more consistent code (Theo knows his code best) and sometimes be faster (poor PRs need a lot of back-and-forth to get them to right quality), it also has 2 major drawbacks:
- it leaves PR branches hanging - logic of the code gets incorporate, but not from PR, so PR commit/branch stays open forever and original contributor needs to manually delete it after a while.
- it discourages people from contributing by actually writing the code - from contributors perspective, if I see my code not being accepted directly, but more as a guideline for what needs to be done, I might as well just write my idea in English and let someone else deal with coding. This doesn't quite help scalability.

To make sure code compiles, Theo uses TravisCI coupled with PlatformIO. This works pretty nicely and every contributor is notified if proposed PR passes build system or not.

Issues found with firmware, in use or in coding, are reported via [GitHub Issue tracker](https://github.com/arendst/Sonoff-Tasmota/issues) and number of people seem to be looking at it and replying back. Discussion is mainly around 'how to use Tasmota with xyz', with problem reports, feature requests and code inquiries following on a long tail. Labels and flags are not used that much, so you can't filter issues by them.

Documentation is Wiki based, and is mainly generated by community itself. This leads to a some outdated or less accurate documents, but, with open policy, Theo encourages anyone to improve documentation and many people actually do, right from Github interface. This [Ikea-effect](https://en.wikipedia.org/wiki/IKEA_effect) might be a reason why Tasmota is so popular.

## ESPurna

Xose hosts ESPurna on [Bitbucket.org](https://bitbucket.org/xoseperez/espurna/), and maintains couple of branches, 2 of them being important:
- dev - being used for daily work
- master - "stable release" branch

Xose also accepts PRs against dev branch and is also almost always reworking the code from other contributors, with same drawbacks as explained above.

While `.travis.yml` file exist in code repository, I was not able to find any actual integration into CI system, leaving build process a bit in the dark. TravisCI doesn't directly integrate with Bitbucket so I would say it's not used, but some other FOSS hosted systems do, and Xose might have the option of using his own CI for checking the builds. In either case, PRs don't seem to be checked.

Issues are reported in Bitbucket's issue tracking system, and Xose is using labels very extensively, classifying the issues for searching, filtering and possibly reporting. Most of the issues are in 'how to use ESPurna', but there are quite a few that deal with bugs, code, programming and overall architecture.

Documentation is weakest point of ESPurna - it is Wiki based, but unlike Tasmota, Wiki for Espurna is closed and requires PR to edit it. This why, all the modifications to Wiki are made by Xose himself, which limits the width, scale and quality, and misses an opportunity to capitalize on community efforts.

## ESPeasy

ESPEasy is the only project that has true OpenSource spirit in it. Although initially lead by LetsControlIt, it's now managed by elected member of community and code is being contributed by ~20 different contributors.

Code is hosted on [Github](https://github.com/letscontrolit/ESPEasy) where issue tracker is also located. Code has only one active branch called `mega` that is being used by both development and stable code.

Community around ESPEasy uses (Github issue tracker)[https://github.com/letscontrolit/ESPEasy/issues] for reporting issues and  most of the issues are development related, as ESPEasy also runs a (user forum)[https://www.letscontrolit.com/forum/] that is used for user support.

ESPEasy uses [TravisCI](https://travis-ci.org/letscontrolit/ESPEasy) for Continuous Integration and every PR is checked against rest of the code. Repository also includes `test/` directory, but it, unfortunately, doesn't include framework for automatic testing of the code.

Documentation is not the strongest point of ESPEasy and most user-support is being handled by forum. In theory, Wiki exists, but it's maintained less frequently and by less people than the one of Tastmota.

# Code quality

## Tasmota

By looking at Tasmota code, one can tell it's a hobby project that started with one goal in mind, but found itself growing larger than originally though, and into directions not anticipated in the beginning.

Code has consistent formatting style, and few releases ago, Theo decided to switch to [Google styleguide](https://google.github.io/styleguide/cppguide.html), which further helped with a lot of naming issues. It's almost "pure C" and only uses function overloading from the whole C++ set of features.

There's not a lot of comments in the code, making some parts very hard to follow, especially when global variables are used. Speaking of which, Tasmota uses quite few of those (including a global MQTT and log buffers), which requires extra caution during coding.

Really irritating stuff are some of the old functions that use literal numeric parameters, like `MqttPublishPrefixTopic_P`. You will find a lot of these in the code:
```
MqttPublishPrefixTopic_P(0, S_RSLT_POWER);
...
MqttPublishPrefixTopic_P(2, PSTR(D_RSLT_INFO "1"));
...
MqttPublishPrefixTopic_P(2, PSTR(D_RSLT_SENSOR), Settings.flag.mqtt_sensor_retain);
...
MqttPublishPrefixTopic_P(5, PSTR(D_CMND_DIMMER));
```
Homework: try remembering what does 2 or 5 mean.

Tasmota has internal, poor-mans task scheduler - hardcoded set of loops that execute every often, invoking certain periodic functions. Code block is located in `sonoff.ino` and might be a bit hard to digest at first, but apparently works fine.

Codacy gives Tasmota [A mark for code quality](https://www.codacy.com/app/lobradov/Sonoff-Tasmota?utm_source=github.com&utm_medium=referral&utm_content=lobradov/Sonoff-Tasmota&utm_campaign=Badge_Grade), with default checks (excluding libraries) reporting only 6 issues.

To support localization, Tasmota uses language files located in `sonoff/language` directory, which are really big `.h` files with defined strings. This means you will not find a message string in any of the Tasmota code, but would need to look for D_... to understand where certain error was generated.

### Code organization

All code can be found in `sonoff/` flat-directory, with only language files belonging to a separate subdirectory.
Most of the code is still in main `sonoff.ino` that, by now, grew to 2700 lines dealing with a lot of stuff, making a code a hard to read and manager around.

User configuration is put in `user_config.h` and it's override `user_config_override.h` (which is in .gitignore) and boards and board types are defined as enums in `sonoff_template.h`

Recently, Theo started splitting code into pieces, and envisioned a concept of modules, which are contained in separate files:
- `xsns_*` - sensors, like ds18b20, DTH/STH, INA219 and similar
- `xdrv_*` - drivers, for abstract things like "light", "energy", "433 bridge" and similar
- `xplg_*` - plugins, mainly for fancy stuff, like WemoHue emulation or WS2812

 (actually, modules exist for quite some time, but have recently underwent a major rework).

 Transition is still not done, and most of the code is sitting between `sonoff.ino`, `settings.ino` and `webserver.ino`. Hopefully, this would change and we'll see more organized code aroud.  

 This actually might be a good start for any contributor looking to help.

### Libraries

Tasmota uses number of "standard" Arduino libraries, but for some reason, Theo decided to include a copy of libraries in the source tree, rather than reference needed library by exact version. (AFAIK, this is only needed for MQTT library which Tasmota modifies).

Most of the stuff used is something average Arduino/ESP8266 developer should be familiar with - `PubSubClient`, `ArduinoJson`, `I2Cdevlib`, few Adafruit libs, `IRremoteESP8266` and similar.

Theo included only one of his own libraries - `TasmotaSerial` as a rewrite of existing libraries for making code slimmer.

### What I liked

Tasmota is compiled without SPIFFS, and uses Theo's own nice trick for saving some space - SPIFFS itself consumes 32k, which is considered an overhead, so instead of saving states and configuration onto SPIFFS, Theo has made a function that directly accesses flash and uses smaller circular buffer to save necessary stuff. This way, flash is not worn out so quickly and some additional space is available for code.

Another gem I found - Theo uses very nice rule for `if ( ) { };` which I found very useful:
```
// BAD - instead of:
if (variable == literal) { ... };
// GOOD - do:
if (literal == variable) { ... };

// example:

// BAD: instead of :
if ( state == D_OK ) { ... };
// GOOD: do:
if (D_OK == state) { ... };
```

Why is this nice? If you ever make a mistake and put = instead of ==, first case will just silently compile and leave you searching for error (value = literal is always true), while second case will cause compilation error as you can't assign value to a literal, and you'll spot the problem right away.

## ESPurna

ESPurna code is really a sight for sore eyes - just by looking at the source directory, you can see Xose is very well organized and very experienced in software development, and its really great to work on a project so organized as ESPurna is. All code is split into individual files doing exactly what the files is supposed to represent (looking for MQTT code? it's in `mqtt.ino`. Guess where WiFi code is.) with main `espurna.ino` being just a glue that ties it all toghether in ~400 lines of code.

Those 400, and rest of 19k lines of code are very readable and very easy to understand, even without looking at surrounding code. Core code is more or less 'pure C', while sensors (described below) are implemented as C++ classes.

ESPurna is also not using SPIFFS, but is managing it's own Flash read/writes for configuration, state information, and very interestingly, Web server static files.

ESPurna uses PlatformIO for building, but for WebUI, platformIO will invoke gulp to build necessary Javascripts and create a static WebUI that will be uploaded as part of build.  

Codacy is reportng [A mark for code quality](https://www.codacy.com/app/lobradov/espurna?utm_source=lobradov@bitbucket.org&utm_medium=referral&utm_content=lobradov/espurna&utm_campaign=Badge_Grade) though number of overall code issues is in the range of 100s, and is mainly coming from inconsistent use of quotes in Javascript and from quick & dirty hacks Xose introduced into (unused) `build.sh` and `memalyzer.sh`. Ignoring `.js` and unused `.sh`, number of issues would drop down to the range of 10.

### Code organization

Each file is, further, very well split into areas: global declarations, "internal" utility functions (starting with _) and "external" functions ("internal" / "external" being a matter of naming and logical convention).

Whole code is organized in modules which are chosen using compile flags and individual `module_setup()` and `module_loop()` functions being invoked from `espurna.ino`. This implies very simple task manager - all modules are called sequentially in every main loop(), and it's up to them to decide how often will they run. Sadly, most modules do run in every loop, but real world example showed it's not causing any slowdowns.  
Still, nicer task management would be good.

Apart from modules, Xose created a framework for sensors, located under `sensors/` directory. Each sensor is extension of BaseSensor class that already defines number of methods, including sensor initialization, reading number and ID of sensors, reading their values and status, or getting and setting their configuration. Framework is very nice and allows anyone to just add sensor related code without looking at rest of the ESPurna codebase.

WebUI code is located in `html/` directory, containing some simple `.css` and `.html`, but some not-so-cleanly written `.js`, as well as binary images, located under `html/images`.

### Libraries

Focusing on firmware only, ESPurna uses few well known libraries like `ArduinoJson`, `PubSubClient`, `OneWire` and others, but Xose also decided to include many of his own libraries, such as `noFUSS`, `JustWifi`, `debounceevent` and similar, some of which override functionality provided by normally-expected libraries to simplify them or provide similar functionality is smaller footprint.

This is great and works great, but may be a bit hard on beginners to ESPurna, as these libraries are not well known (nor always well documented).

All libraries are fetched through PlatformIO system and many are nailed in version down to a commit so builds aren't broken by changes in upstream code. This is very welcomed.

WebUI is whole different ball game - first, it's packaged using gulp with quite a few dependencies, and then uses a number of Javascript libraries, including Xose's own `custom.js` to work with UI. Unlike rest of the code, this is quite messy and crammed into a single file, so it's not really easy to manage. I guess it's not that important, hence not a lot of focus to sort it out, but this is the reason I have 4 out of 5 stars on code readability category in the table above.

### What I liked

Xose did amazing job in creating a rule-based structure for creating new modules, and, of course, a BaseSensor class for sensors. Looking at other modules, it's very easy to pick up the logic and requirements and create your own module and sensor.

Another big item I discovered recently is a very smart use of Flash space for serving proper Web content. Basic idea is to wrote webUI frontend code, as if you would run it off of a regular server, use gulp to pack it into archive and then embedd all of that into PROGMEM.  
Xose described the process in [his blog](http://tinkerman.cat/embed-your-website-in-your-esp8266-firmware-image/) and I suggest you spend some time on this - very interesting and smart. 

## ESPEasy

Looking at the code, you can see this is the first code that served as inspiration for Tasmota that looks like slightly side-evolved copy of ESPEasy style and logic. There's a lot of global variables and a lot of functions that just magically use them, so you should really be careful when using those, but on the other side, ESPEasy evolution took a turn at some point and created very nice plugins I'll describe later.

ESPEasy is written in 'pure C' and doesn't use any of the C++ stuff. Code is mostly clean but not a lot commented and leaves potential contributor with a bit of initial head scratching.

Codacy gives ESPEasy a [Code quality rating B](https://www.codacy.com/app/lobradov/ESPEasy?utm_source=github.com&utm_medium=referral&utm_content=lobradov/ESPEasy&utm_campaign=Badge_Grade), with 67 issues as of today, and a lot of them in security category. Still, review of the issues would show they are found in build helper tools like `esptool.py` and `test/node.py` and not in the firmware itself.

### Code organization

All code is located in `src/` directory and is split into number of files. Main file `ESPeasy.ino` starts with a very long list of defines, so I'm wondering why it wasn't split into `.h` and `.ino`, but I guess there's a legacy reason for that.

It continues into a task scheduler that clearly organizes different tasks according to how often they should be invoked. There's a loop for tasks that should run 50 times per second, 10 times per second, every second and finally every 30 seconds. While not perfect, this is still best task scheduler mechanism available in all 3 firmware options.

Rest of the code is split into few files dealing with major parts of core system (`Hardware.ino`, `Networking.ino`, `Serial.ino`, `WebServer.ino`, `Wifi.ino` and my favorite, 3000+ lines of code `Misc.ino`, which is my main and biggest concern with ESPEasy), while non-core parts are organized into:
- `_C*.ino` - Controller plugins
- `_N*.ino` - Notification plugins
- `P*.ino` - Plugin plugins :) I would describe them as devices...

Loading of different plugins is handled by `__Plugin.ino` (and derivatives) and each of these plugins has a predefined structure, with each plugin entry point being called by a number with a standard, similar (but different) than ESPurna.


### Libraries

ESPEasy uses a number of well known Arduino libraries, like `ArduinoJson`, `PubSubClient`, `IRremoteESP8266`, `LiquidCrystal_I2C` and similar libs, together with number of libs from Adafruit and SparkFun. Unlike Tasmota and ESPurna, where libraries are compiled only if relevant functionality is used, with ESPEasy all libs are compiled in the main image. I assume some optimization flags of compiler discards truly dead code, since ESPEasy images are not that bigger than those of Tasmota.

Just like Tasmota, guys at LetsControlIt decided to bundle all necessary libraries as part of the repository instead of referencing it via PlatformIO or even as Github link. This results in pretty big repository with libraries that are slightly outdated and require manual update. Still, it all "just works" so no big complaints here.

### What I liked

Thing I liked about ESPEasy is the plugin system. Take a look at following plugin structure to see why:
```
boolean CPlugin_xxx(byte function, struct EventStruct *event, String& string)
{
  boolean success = false;
  switch (function)
  {
    case CPLUGIN_PROTOCOL_ADD:
      {
        Protocol[++protocolCount].Number = CPLUGIN_ID_009;
        Protocol[protocolCount].usesMQTT = false;
        Protocol[protocolCount].usesTemplate = false;
        Protocol[protocolCount].usesAccount = true;
        Protocol[protocolCount].usesPassword = true;
        Protocol[protocolCount].usesID = false;
        Protocol[protocolCount].defaultPort = 8383;
        break;
      }

    case CPLUGIN_GET_DEVICENAME: {... }
    case CPLUGIN_PROTOCOL_SEND: { ... }
    case CPLUGIN_PROTOCOL_RECV: { ... }
```

"Header" (`function == CPLUGIN_PROTOCOL_ADD`) allows declaration of the plugin and its needs, which are later used for scheduling and polling purposes. For example, plugins that don't use MQTT will not be considered as potential actions for MQTT messages, making internal mechanics quite easy.

While logic is good and just works, overall numbering and hiding behind `function` value is a bit awkward, but then again, it allows contributors to clearly focus on one and only one module.

# Summary

All 3 options are good base for contribution and provide healthy base for your hacking and tinkering, with drawbacks that one should get used to.

None of the options has a design document nor a properly defined contribution policy, but then again, just by monitoring few days of emails and looking at the code, you can pick up most important stuff and just play by the ear.

**ESPurna** would be my favorite choice, due to code cleanness and organization, if it wasn't for BitBucket Wiki and Bitbucket in general, that, in my opinion, restricts possibilities of collaboration. Maybe it's a matter of configuration, but reality is that not a lot of people find it easy to join ESPurna development, and my guess is that it is not due to code or feature-set, but about tooling that's simply not sexy enough.

**ESPEasy** would be my second choice, due to still-good organization, code cleanness and overall attitude towards contributors, if it wasn't for functionality and usability that just doesn't work for me. It's still a great platform, but, as I explained in my [other post](https://lobradov.github.io/FOSS-Firmware-comparison-overview/), it just doesn't work for me and my main use-case.

**Tasmota** has a lot of drawbacks and a lot of specifics in the code that sometimes drive you nuts, but overall package is manageable once you get used to details and gives a nice platform for contributing, with a healthy community that would test the code and report back with issues. Theo could improve contribution policy and refactor great deal of code, but it's a matter of priority.

What did you choose? Why?
