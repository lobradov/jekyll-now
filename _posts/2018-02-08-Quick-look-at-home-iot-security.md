---
layout: post
title: Quick look at home IOT (in)security
date: 2018-02-08
categories:
tags:
comments: true
---

IOT is a great thing: you buy a cheap controller, program it, see it working and, all of a sudden, start depending on it in your daily life. But, what if someone gets to your network and starts turning lights on and off? What if it's not only lights, but irrigation systems, garage doors or worse?  

# Current state of Home IOT security

Quick look at Shodan shows us number of issues with today's state of Home IOT security: As of right now:
- at least 1516 homes are open to [unrestricted, probably unauthorized and open remote management](https://www.shodan.io/search?query=espurna%2F+OR+cmnd%2F+OR+espeasy%2F+OR+sonoff+OR+domoticz). Refine a search and you'll probably get more.
- 950 families are openly publishing their [GPS-accurate whereabouts](https://www.shodan.io/search?query=owntracks%2F)
- 129 garages [can be open remotely over the internet](https://www.shodan.io/search?query=garage+port%3A%221883%22) (you need to have an account with Shodan for this one to display)

> Shodan provides you the ability to see who are these poor souls who didn't get the concept of security right. Feel free to look at the list, learn something from it, but don't abuse your knowledge. Don't be an ass.

# Do you really need remote access?

Main question you have to ask yourself is - do I *really* need remote access to my home? Do a bit of a personal habit analysis and risk management and figure out if it's really worth it.

Sure, it's great to brag to your friends about being able to fire a light in your house using your phone, but would you really use it often?  
I found out that I'm not using it almost at all.

I also found out that my Wifi extends a bit around my apartment, so I get into the range and connect early enough that system can detect me when I'm close to my home and automatically turn on welcome lights.

This is a balance that I decided to make: my network is not open to internet and risk is minimized, while I still get a lot of things done.

# "Of course I need remote access!"

If you are absolutely sure you do need to open your home network to external access, let's see what is that we can do to make your setup more secure. I'm assuming you locked your firewall and only opened necessary ports, and I'm also assuming you either have a static IP with your provider (less likely) or have configured one of the dynamic DNS services.

## Never expose your devices

Not a single firmware for our favorite devices is made to be directly exposed to big-evil-internet, so you should never, ever expose devices directly. Instead, devices should talk to MQTT brokers and home automation software, which you will, selectively, and depending on your use-case, expose outside.

## Turn off what you don't need

Many firmware and software come with options you might not need. ESPurna has a telnet interface, and ESPEasy offers UDP device-to-device control protocol. If you are not using them, disable them (or even better, don't compile them in - you don't need a dead code).

Home automation software is even more complex to manage, as number of options is really high. Start with empty blank config and then only add what you need.

## Use authentication and logging

Comes without saying - first step is to enable authentication on all devices / services you are running. Apart from the ones that you (as human) are using, like Web Interface, don't forget those interfaces or functionality that you personally don't interact with.

Most common mistake is to enable MQTT access without user/pass (because, hey, who will configure user/pass for each of the devices you have?) which is a great mistake.

Another mistake is to use simple or default credentials, without any logging or brute-force throttling. This way, scripted guessing can happen for days before you notice something strange happens to your setup.

## Let's encrypt your access

Head over to [letsencrypt.org](https://letsencrypt.org/) and get yourself a free, fully valid TLS certificate. Let's Encrypt is a public, free and open certificate authority with a mission to provide free TLS for everyone owning a domain name. Given you configured dynamic DNS, you qualify as domain owner, and are entitled to free TLS certificate.

With those certificates, you can configure your MQTT and your home automation to use TSL. Every option out there supports TSL: be it Mosquitto, EMQTT, Domotics, Home-Assistant, Node-RED, OpenHAB or any other software.

## Test

Run `nmap` as simple, but also `nessus`, `burp-site` or any other security scanning tool that might give you more information about your setup. Test from both inside and outside of your home Wifi and see what are you actually offering to evil-lurkers on the internet. You'll be surprised that every setup has "something" that is not supposed to be there, but it is.
