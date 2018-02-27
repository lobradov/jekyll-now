---
layout: post
title: Multi-arch Home IOT components
date: 2018-02-26
categories:
tags:
comments: true
---
Seems multi-arch images are not taking off as fast as I hoped, so I was forced to make few of my own to accomodate for Home-assistant, Mosquitto and Letsencrypt certbot running on Raspberry Pi (ARM32v7) and on Orange Pi Prime (ARM64v8).

# Home-Assistant

I took original home-assistant images and just provided a single fat manifest for them.

So, instead of pulling different images for different platforms:
```console
# (on Linux)
docker run -d homeassistant/home-assistant
# (on Raspberry Pi)
docker run -d homeassistant/raspberrypi{1,2,3}-homeassistant
# (on Orange Pi Prime):
docker run -d homeassistant/aarch64-homeassistant
```

you can just do:
```
docker run -d lobradov/homeassistant
```

and leave the magic of choosing right image to to Docker engine.

This magic is called fat manifest and is basically a pointer to different images, with some metadata (annotations) to help Docker engine pick the right one.

Scripts that build that are here:
https://github.com/lobradov/docker-homeassistant
(this requires my framework for building multi-arch images, as described here: https://lobradov.github.io/Building-docker-multiarch-images/)

#  Mosquitto

Inspired by original images, which are built only for X86_64 (amd64), I rebuild mosquitto to be multi-arch.

Usage is the same as original image, so:
```console
$ docker run -it \
  -p 1883:1883 \
  -p 9001:9001 \
  -v mosquitto.conf:/mosquitto/config/mosquitto.conf \
  -v /mosquitto/data \
  -v /mosquitto/log \
  lobradov/mosquitto
```

Scripts user to build are here:
https://github.com/lobradov/docker-mosquitto
(this requires my framework for building multi-arch images, as described here: https://lobradov.github.io/Building-docker-multiarch-images/)

# Letsencrypt certbot

Same as Mosquitto, original Certbot images are build only for X86_64 (amd64), so I rebuilt it to be multi-arch.

Usage is the same as original image, so:
```console
docker run -it --rm -p 443:443 -p 80:80 --name certbot \
  -v /etc/letsencrypt \
  -v /var/lib/letsencrypt \
  -v /var/log \
  lobradov/certbot certonly
```

Scripts to build are here:
https://github.com/lobradov/docker-certbot
(this requires my framework for building multi-arch images, as described here: https://lobradov.github.io/Building-docker-multiarch-images/)
