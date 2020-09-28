---
layout: post
title: My new project - cave telemetry
date: 2020-09-28
categories: lora
comments: true
---
I'm starting a new project related to my new hobby, and will be writing notes as I progress. 

# Hobby 

In corona times, travel options are limited, so I took on a new hobby that I could excersise in my home country - speleology. It's interesting, fun and physically demanding as a hobby, but also opens quite a few interesting nerdish questions. As my instructor and teacher would put it: 

> speleology is a perfect way for a layman to do some serious science 

After going into few caves, I already saw few open questions that current speleology (at least in my country) can't answer, so I thought I'd try to help with those: 
- Caves and pits have their own micro-climate which varies depending on the conditions outside (rain, snow, temperature) and on part of the year. Basic and widelly applicable models exist that explain these changes (rain on the outside raises relative humidity in the cave after some time) but exact correlation is not measured nor empirically proven. Interesting thing is with exceptions to the model, that might suggest something interesting to explore. Without data, you can' tell.
- Level of different gasses in caves depends on number of factors, including CO2 offgassing (that makes speleothermes) and rotting of organic matter that fell into caves and pits. Again, model exists, but exact measurement - not really. 
- (many) expored caves are assumed to be connected to eachother and explorers are trying hard to determine which caves are part of a same system. One proposed way to determine that is to correlate changes of cave parameters (rH, CO2 levels, temperature changes, other gases) and deduce from there.  

I'm sure that there are many other questions to be answered, but even for the few above, I figured out that a network of sensors that periodically captures key parameters will be very sufficient.

Now, there are problems: 
- Cave entrances are typically far away from civilization and in many cases not covered by a 3G/4G signal, not to mention electricity. I'm not speaking about "show caves" (ones open for general population)... 
- 3G/4G signals do not penetrate undeground very well :) 
- Cell network modules consume a lot of power and can't really operate on batteries for extended periods of time. Sure, you can place some solar panels to units that are out on a daylight, but what about units inside the cave? Note that cave can be a system of few hundred or even thousant meters.

Enter LoRa

# LoRA

You've probably heard about LoRa, which apparently stands for "Long Range" network. It's a class of Low Power Wide Area Networks (LP-WAN) that run on an unlicensed (public use) part of the spectrum and typically do not require special permit to operate. Because of that, they have legal small limits for transmit power and for "air time". LoRa is designed with few things in mind: 
- Low power consumption - ability to run sensors on battery for extended (claimed 10 years) time
- Long range - thanks to high sensitivity and few radio tricks, LoRa claims extra long range on a small transmit power, reaching up to 10-12 km in ideal conditions. Urban settings reduce that to few kilometers, still being very long-range, given the power usage.
- High resiliency - LoRa is made to pick up signal from both direct line of sight, as well as from reflections and refractions, implying good coverage in urban areas. I'm counting that geometry and materials of caves are similar to geometry of urban concrete mazes and am hoping that signal will bounce off the walls far enough to cover small to medium sized caves. 

## Architecture 

LoRa has 3-4 classes of devices: 
- **Gateways** - device that connects LoRa network to internet. On Internet side, LoRaWAN gateways can have ethernet, Wi-Fi, 3G/4G, or other well known technologies, but on LoRA side, gateways have special gateway chips capable of receiving multiple channeles in parallel, effectivelly acting as concentrators (which is the official name of this class of devices). Gateways are meant to be placed in known locations, to be properly powered and have well-placed antenas. LoRa network can have multiple gateways, covering either greater range or allowing for fancy stuff like node location tracking (by triangulating nodes using their signal strenght against knwon LoRA gateway locations). 
- **Nodes** - end devices that are sending some useful data. They have LoRA transmiter (modems) on LoRa side, and whatever sensors on the other. Nodes (or terminals) are made with power usage in mind, as they will be placed wherever some mesurement is needed.
- **Repeaters** - subclass of Nodes, meant to relay the messages, and thus extend the range. Idea is that one node can receive a message broadcasted by other, store it in it's own memory and then repeat it when channel gets free. To avoid broadcast storms, some topology learning will be needed, but I have not found anything useful on this topic. This might be one of the problems I'll tackle during this project of mine. In a cave environment, I imagine a series of repeaters that will relay the message frmo furthest node located deep in the cave, all the way back to surface, where signal could be picked up by real gateway and sent to internet.

# What's next

During this project, I'll have to learn (and write) about: 
- What are the good commercialy available gateways?
- How to determine where is the best place for them in a mountaneus setting?
- How far do gateways reach in that setting?
- How to setup TTN (Cloud service that exposes your LoRA network messages via some API)?
- What LoRa modem to choose for my purpose?
- How to build LoRA node to measure relative humidity, temperature, CO2 and organic volatile gasses?
- How does LoRA behave under ground? 
- How to build solar-powered repeaters? 
- How to optimize usage of batter-operated combined repeaters and sensor nodes?
- ... 

Wish me luck and leave me pointers or comments with something you think would help.




