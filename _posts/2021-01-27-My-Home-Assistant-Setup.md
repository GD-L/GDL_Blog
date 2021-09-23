---
layout: post
title:  "My Home Assistant Setup"
date:   2021-01-27
tags: [Home Assistant, Docker]
category: [Homelab]
---

<div align="center">
  <img src="https://raw.githubusercontent.com/home-assistant/assets/master/logo/logo-pretty.png" height ="220" align="center">
</div>

## Home Assistant Setup

When I first started experimenting with [Home Assistant](https://www.home-assistant.io/) I started with a simple `docker run` and _thankfully_ learned about `docker-compose` not too long after. Currently my compose file is pretty straight forward. I pass though the [HUSBZB-1](https://www.amazon.com/QuickStick-Combo-HUSBZB-1-Nortek-Cert/dp/B0157GOEA8/ref=sr_1_4) which requires two passthroughs: one for Z-Wave and one for ZigBee.
<!--more-->
```yaml
version: '3.1'
services:
  homeassistant:
    container_name: 'home-assistant'
    image: homeassistant/home-assistant:stable
    network_mode: host
    volumes:
      - /docker/homeassistant:/config
    devices:
    #Passthrough USB Stick for Z-Wave & Zigbee
      - /dev/ttyUSB1:/dev/ttyUSB1
      - /dev/ttyUSB0:/dev/ttyUSB0
    env_file: /docker/docker_envs.env
    restart: unless-stopped
```
## Components

### Hardware

* ~~[Wink Hub 2](https://www.wink.com/products/wink-hub-2/) - This currently serves as my hub for most components, specifically for Z-Wave and Zigbee devices.~~ 
* After Wink decided they wanted to start charging for their services, I threw the hub in the trash can, and got a HUSBZB-1, that now serves as my Z-Wave and ZigBee hub.
* Z-Wave/Zigbee Devices
  * [Sylvania Smart Plug](https://consumer.sylvania.com/our-products/smart/product-info/zigbee/sylvania-smart-zigbee-indoor-smart-plug/index.jsp) (2x)
  * [GE Z-Wave In-Wall Switch](https://byjasco.com/products/ge-z-wave-plus-wall-smart-switch-white-toggle)
  * [Wink Door Sensor](https://www.wink.com/products/wink-doorwindow-sensor/) (2x)
  * [Wink Siren and Chime](https://www.wink.com/products/wink-siren-and-chime/)
* [Ecobee Lite3 Thermostats](https://www.ecobee.com/ecobee3-lite/) (2x: Upstairs, Downstairs)
* [WeMo Smart Plug (F7C027)](https://www.belkin.com/us/Products/smarthome-iot/c/wemo/)
* [Roku Streaming Stick+](https://www.roku.com/products/streaming-stick-plus)

### Sensors & Integrations

* [piHole](https://www.home-assistant.io/components/pi_hole/)
* [Glances](https://www.home-assistant.io/components/glances/)
* [UPnP](https://www.home-assistant.io/components/upnp/)
* [Vacuum](https://www.home-assistant.io/components/template/) Uses a template sensor to gather `battery_level` and `status` from state attribute.
* [Weather](https://www.home-assistant.io/components/darksky/) Uses DarkSky for additional weather sensors.
* [Plex](https://www.home-assistant.io/components/plex/)
* [Ecobee](https://www.home-assistant.io/components/ecobee/)
* [DarkSky](https://www.home-assistant.io/components/darksky/)
* [Roku](https://www.home-assistant.io/components/roku/)
* [Ecovacs](https://www.home-assistant.io/components/ecovacs/)
* [Speedtest](https://www.home-assistant.io/components/speedtestdotnet/)
* [Owntracks](https://www.home-assistant.io/components/owntracks/)

## Automations
[Automations](https://github.com/gd-l/homeassistant/tree/master/automations) moved to directory and split out based on functionality:
* [Lights](https://github.com/gd-l/homeassistant/blob/master/automations/lights.yaml)
* [Sensors](https://github.com/gd-l/homeassistant/blob/master/automations/sensors.yaml)
* [Telegram](https://github.com/gd-l/homeassistant/blob/master/automations/telegram.yaml)
* [Thermostats](https://github.com/gd-l/homeassistant/blob/master/automations/thermostatautomations.yaml)
