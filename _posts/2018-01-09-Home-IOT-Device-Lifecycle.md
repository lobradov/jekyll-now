---
layout: post
title: Home IOT device lifecycle
date: 2018-01-09
categories:
tags:
comments: true
---

Once you decide to install devices in your network and start using them in your daily interaction with your home, they start having their own lifecycle. You don't have to feed them and sign them lullabies when going to sleep, but periodic check and maintenance would ensure your home runs great and you have a lot of fun using it.

# IoT device Lifecycle
Lifecycle is usually defined as partially continuous process of maintaining devices working state while its in service in your network. Roughly speaking, IoT devices have lifecycle as depicted on the diagram below:

It all starts with Flashing firmware onto a newly purchased board, and then continues with connecting a board to a network, configuring attached sensors and actuators, configuring MQTT and/or API parameters and performing basic testing of integration with your favorite Home Automation software.

After you got that working, it's time to relax and enjoy your automated home, as things "should" just work from now on, but since our old friend Murphy never sleeps, so we'll need to be prepared, by  keeping an eye out for problems and having a backup of the config at hand.

Once there will be issues (and there will be issues), we'll need to properly identify them, debug and see how do we (or someone else) fix them. Maybe issue is in configuration, maybe in firmware itself.

Assuming it's in firmware, you'd need to report it to developers and wait for a fix. Once fix is provided, we need to upload new version of firmware, and maybe restore saved config, in case new firmware didn't load the existing one from the flash.

Of course, all of this is theory, but at least gives you an idea on what tasks you'd have to think of and what you might want to automate in your home.
