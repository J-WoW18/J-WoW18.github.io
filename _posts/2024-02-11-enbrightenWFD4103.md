---
layout: post
title: Enbrighten - WFD4103
date: 2024-02-11 12:00:00 -500
categories: [homelab, IOT]
tags: [bk7321t, iot, enbrighten, wifi, bt] 
---

* Single smart outlet plug by Enbrighten picked up at the local auction return this week.
* Below captures my thought process and notes as I attempted to get this flashed over to ESPHome and Home Assistant. My initial thoughts about the device turned out to be wrong upon opening it up. 
 
# Device Info
* Model: WFD4103
* FCC ID: QOB-WFD4103
## Acquisition
* Another cheap win at only $3. These go for $10-20 new.

## FCC lookup
* Loking up the FCC ID online
	* https://fcc.report/FCC-ID/QOB-WFD4103
	* Change of ID letter point to another FCC ID
	
		 ![29109751b3d70c592f3a055c1cbae130.png](/assets/29109751b3d70c592f3a055c1cbae130.png)
		
	* https://fcc.report/FCC-ID/2AMV5-SK539-W-A
	
## Internal Photos
* Internal photos found for the older FCC ID

![1b556f2f469f96143ce863dd281b069d.png](/assets/1b556f2f469f96143ce863dd281b069d.png)

* Looks like it's a CB2S module 

![760d48cca6a73ef6dfa053bacd7e5fd0.png](/assets/760d48cca6a73ef6dfa053bacd7e5fd0.png)

## CB2S Module
* Pin diagram available online
	* https://developer.tuya.com/en/docs/iot/cb2s-module-datasheet?id=Kafgfsa2aaypq

# Wireless Exploits
* Since this is a single plug and his held together by screws and compression... I'm hoping the tuya-cloudcutter will work instead of having to potentially destroy and rip apart the plaster case.
## Tuya-Cloudcutter
* https://github.com/tuya-cloudcutter/tuya-cloudcutter
	* "This repository contains the toolchain to exploit a wireless vulnerability that can jailbreak some of the latest smart devices built with the bk7231 chipset under various brand names by Tuya. "
* run `sudo ./tuya-cloudcutter.sh` and let it build a fresh docker image

![3a3b084f216f72bb7ed46ab36223cf63.png](/assets/3a3b084f216f72bb7ed46ab36223cf63.png)

![6e572b7c7edf5773a5093c1f844a3f63.png](/assets/6e572b7c7edf5773a5093c1f844a3f63.png)

* no profile for the 4103, but a 4103E. Upon selecting however, shows a BK7321T firmware but if this is a CB2S module then it should be an "N"

![7d02fb79a3b1a26eb4847484daac07ee.png](/assets/7d02fb79a3b1a26eb4847484daac07ee.png)

* Looking at the other option it is a BK7231N firmware

![031a194321d4dd53e8f9e0d215792974.png](/assets/031a194321d4dd53e8f9e0d215792974.png)

![1b1382547e87fb46971b5117af609540.png](/assets/1b1382547e87fb46971b5117af609540.png)
## Results
* Not succesful trying both flash profiles. Most likely these two other pre-built profiles have different keys or something that is different enough it wont autheticate and allow the re-write. Guess it's on to opening it up....

# Teardown & Connections
## External Photos
* Turned on the poor mans hot plate to try to heat and soften the glue before trying to separate the top and bottom parts of the plug.
* ![25849062d36a2df523bfac1822925ab2.png](/assets/25849062d36a2df523bfac1822925ab2.png)

* Heat helped a little but still required a good bit of cutting through and prying up

![3677db6f1ad0423dbe89e160ef559154.png](/assets/3677db6f1ad0423dbe89e160ef559154.png)

## Internal Photos
* It's a `WB2S` not a `CB2S` like I was thinking from the FCC research...

![0692a2d5afe569b101743be27835e219.png](/assets/0692a2d5afe569b101743be27835e219.png)

* WB2S pinout

![52a41b64fa4c01c2a179a54a62f9c6a3.png](/assets/52a41b64fa4c01c2a179a54a62f9c6a3.png)

* Connecting it all up

![19faef3801a5db71070309cb12b5c3f9.png](/assets/19faef3801a5db71070309cb12b5c3f9.png)

# Firmware Dump
* Serial to USB converter connected up to computer and ltchiptool was used to interact with the CB2S module.
	* https://github.com/libretiny-eu/ltchiptool

## Chip Info
* Looking at the chip info

![4f086992d0080895418fdc9507ca8f71.png](/assets/4f086992d0080895418fdc9507ca8f71.png)

## Read Flash
* Firmware saved and read back in to look at pin configuration

![f52bb87e9f5912548ee8b5c84f8e6344.png](/assets/f52bb87e9f5912548ee8b5c84f8e6344.png)

# ESPHome
## ESPHome Config
```yml
esphome:
  name: enbrighten-wfd4103-2
  friendly_name: enbrighten-wfd4103-2
bk72xx:
  board: generic-bk7231t-qfn32-tuya
logger:
web_server:
captive_portal:
mdns:
api:
ota:
wifi:
  ssid: !secret wifi_ssid2
  password: !secret wifi_password2
  fast_connect: true
  manual_ip:
    static_ip: 192.168.xx.xx
    gateway: 192.168.xx.1
    subnet: 255.255.255.0
text_sensor:
  - platform: libretiny
    version:
      name: LibreTiny Version
binary_sensor:
  - platform: gpio
    id: binary_switch_1
    pin:
      number: P26
      inverted: true
      mode: INPUT_PULLUP
    on_press:
      then:
        - switch.toggle: switch_1
switch:
  - platform: gpio
    id: switch_1
    name: Relay 1
    pin: P24
status_led:
  pin:
    number: P7
    inverted: true
```
* In Home Assistant and ESPHome, a new device is created matching the device name in the YAML. Device can be installed, downloaded and flashed using LTChiptool.
## ESPHome Operation
* After flashing the device was put back together and then plugged into an outlet. After a few seconds the device popped up on Home Assistant as a new Device. 
* ESPHome dashboard showin operation and individual relay operation

![ec525ec53ced957d747915f3e0cb1872.png](/assets/ec525ec53ced957d747915f3e0cb1872.png)

* Webserver view showing the same individual control of the relay and OTA upload option

![de8d005169c2feff06b2eaa12affb513.png](/assets/de8d005169c2feff06b2eaa12affb513.png)

# Final Thoughts
* Was hoping this would work of Tuya Cloud-cutter instead of having to open and flash it manually. Another simple little smart plug integrated with Home Assistant.
* I will start looking at how to contribute firmware and profiles back to the repo so in the future others can have it working for this model.
