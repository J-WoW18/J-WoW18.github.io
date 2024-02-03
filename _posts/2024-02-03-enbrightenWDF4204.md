---
layout: post
title: Enbrighten - WFD4204
date: 2024-02-03 15:00:00 -500
categories: [homelab, IOT]
tags: [bk7321n, iot, enbrighten, wifi, bt] 
---

# Device Info
* Model # 74517 - WDF4204
* FCC ID: QOBWFD4204

![058897c32dc854df8ef4bddc1050b2fa.png](../_resources/058897c32dc854df8ef4bddc1050b2fa.png)
## Acquisition
* Got this 6 outlet smart outdoor yard stake from the local return auction for $2.50. Normally these go for $10-20.

![9214bdeb77b6b3c3129aa366874ab1c2.png](../_resources/9214bdeb77b6b3c3129aa366874ab1c2.png)

## FCC ID Lookup
* Look up the model number online to get an idea of what to expect inside
	* https://fccid.io/QOBWFD4204
### Internal Photos
![7cc6620ee619cbf3412c1c0694ee4f77.png](../_resources/7cc6620ee619cbf3412c1c0694ee4f77.png)
![9b4a0547f6d00463ecdc42ade6fe8e1d.png](../_resources/9b4a0547f6d00463ecdc42ade6fe8e1d.png)
![b74d7de8b6032e6a3b5eb346a8292119.png](../_resources/b74d7de8b6032e6a3b5eb346a8292119.png)
![ef3195d5b18181be38bcb354c49f4a76.png](../_resources/ef3195d5b18181be38bcb354c49f4a76.png)
![9a2dc3983688e1fad1430294a61f27fa.png](../_resources/9a2dc3983688e1fad1430294a61f27fa.png)
### User Manual
![98a3154230c8767fb147e2b6d44bc923.png](../_resources/98a3154230c8767fb147e2b6d44bc923.png)

## CB2S Module
* Pin diagram available online
	* https://developer.tuya.com/en/docs/iot/cb2s-module-datasheet?id=Kafgfsa2aaypq

| Pin number | Symbol | I/O type | Function                                                                                                                                                                                                                                              |   |
|------------|--------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
| 1          | 3V3    | P        | Power supply 3V3                                                                                                                                                                                                                                      |   |
| 2          | P6     | I/O      | Support hardware PWM and correspond to P6 of the IC                                                                                                                                                                                                   |   |
| 3          | GND    | P        | Power supply reference ground                                                                                                                                                                                                                         |   |
| 4          | P7     | I/O      | Support hardware PWM and correspond to P7 of the IC                                                                                                                                                                                                   |   |
| 5          | RX1    | I/O      | UART_RX1, which is used for receiving user data and corresponds to P10 of the IC. Do not pull it up. By default, the MCU serial port should be in low-level or high-impedance state.                                                                  |   |
| 6          | P8     | I/O      | Support hardware PWM and correspond to P8 of the IC                                                                                                                                                                                                   |   |
| 7          | TX1    | I/O      | UART_TX1, which is used for transmitting user data and corresponds to P11 of the IC. Do not pull it up. By default, the MCU serial port should be in low-level or high-impedance state.                                                               |   |
| 8          | ADC    | I/O      | ADC, which corresponds to P23 of the IC                                                                                                                                                                                                               |   |
| 9          | P24    | I/O      | Support hardware PWM and correspond to P24 of the IC                                                                                                                                                                                                  |   |
| 10         | CEN    | I/O      | Reset pin                                                                                                                                                                                                                                             |   |
| 11         | P26    | I/O      | Support hardware PWM and correspond to P26 of the IC                                                                                                                                                                                                  |   |
| Test point | RX2    | I/O      | UART_RX2, which corresponds to P1 of the IC. This pin is not allowed to use.                                                                                                                                                                          |   |
| Test point | TX2    | I/O      | UART_TX2, which is used for outputting logs and corresponds to P0 of the IC                                                                                                                                                                           |   |
| Test point | CSN    | I/O      | Mode selection pin. If it is connected to the ground before being powered on, enter the firmware test mode. If it is not connected or connected to VCC before being powered on, enter the firmware application mode. It corresponds to P21 of the IC. |   |

* Drawing of the front and back of module

![9998d57df6d15a02be53c27dbfea2246.png](../_resources/9998d57df6d15a02be53c27dbfea2246.png)
![b7ce6b147b92cbaf9fcf6d267a38c2ba.png](../_resources/b7ce6b147b92cbaf9fcf6d267a38c2ba.png)

# Wireless Exploits
* There has been alot of great work on exploiting these IOT devices that are associated with the TUYA system of systems. The Tuya-Cloudcutter software has been updated and figured I'd give this another shot. I had tried to use this a year or so back without luck, maybe this device will be different...
## Tuya-Cloudcutter
* https://github.com/tuya-cloudcutter/tuya-cloudcutter
	* "This repository contains the toolchain to exploit a wireless vulnerability that can jailbreak some of the latest smart devices built with the bk7231 chipset under various brand names by Tuya. "
### Background on the exploit
* https://rb9.nl/posts/2022-03-29-light-jailbreaking-exploiting-tuya-iot-devices/
## DigiblurDIY Guide
* I followed DigiblurDIY's guide to setup a Raspberry Pi 4 (8GB RAM) to work the exploit.
	* https://digiblur.com/2023/08/19/updated-tuya-cloudcutter-with-esphome-bk7231-how-to-guide/
## Total Failure
* After setting all this up... turns out the device when plugged in would not even power on.... had to crack it open anyways to look at the hardware.

# Teardown & Connections
## External Photos
* Gotta love triangle security screws... four of them and the top can come off

![ad55a0d27be2ab45c4248b5778be4ef6.png](../_resources/ad55a0d27be2ab45c4248b5778be4ef6.png)
* looks just like the internal photos from FCC database
 
 ![4d7cc4adb14ffc354636d2a13a29c5f8.png](../_resources/4d7cc4adb14ffc354636d2a13a29c5f8.png)
 * Better look at the CB2S module and the single relay for the 6 outlets
 
 ![d55df1d95fc5391eb63917db3cc9af00.png](../_resources/d55df1d95fc5391eb63917db3cc9af00.png)
 
* Welp.... that might be the issue with it not powering on...

![ab7d82d607f73b696687b5509d636a7e.png](../_resources/ab7d82d607f73b696687b5509d636a7e.png)

* Putting it on the pcb holder and mapping out the pins for the serial to usb connection

![6eb23ca9283fa0e47758c7de32b765c1.png](../_resources/6eb23ca9283fa0e47758c7de32b765c1.png)
* All wired up and connected to the computer

![1b3da10d0a485afd817ba72ba532344b.png](../_resources/1b3da10d0a485afd817ba72ba532344b.png)

# Firmware Dump
* Serial to USB converter connected up to computer and ltchiptool was used to interact with the CB2S module.
	* https://github.com/libretiny-eu/ltchiptool

## Chip Info
![eb202884b3fedfed8cdd6b736e1c2705.png](../_resources/eb202884b3fedfed8cdd6b736e1c2705.png)

## Read Flash
* Firmware saved twice

 ![b1a6ef58f526bbc2f2a49fb9ea17460b.png](../_resources/b1a6ef58f526bbc2f2a49fb9ea17460b.png)
 
 * Reading the firmware with LTChiptool to get pin assignments and configurations
 
![214a31a9016bd2678afbd3228c4f926d.png](../_resources/214a31a9016bd2678afbd3228c4f926d.png)
# ESPHome
## ESPHome Config

``` yml
esphome:
  name: enbrighten_WFD4204_1
bk72xx:
  board: generic-bk7231n-qfn32-tuya
logger:
web_server:
captive_portal:
mdns:
api:
ota:
wifi:
  ssid: !secret wifi_ssid2
  password: !secret wifi_password2
  ap:
text_sensor:
  - platform: libretiny
    version:
      name: LibreTiny Version
binary_sensor:
  - platform: gpio
    id: binary_switch_1
    pin:
      number: P8
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
* After Flashing and power cycling.... device would not integrate with Home Assistant. Router would see the IP address pop up but when attempting to add device into Home Assistant it would not establish a connection.
* Disconnecting the PCB from the holder and plugging in to 120v the device would not power on. Clearly the damage PCB was not completing the circuit to power on the device.

# Final Thoughts
* Was able to read and write to the CB2S module but think the power issues and overall damage to the PCB are going to limit the usefulness of the device.
* Bright side is if I need a CB2S module for another project this is readily available. Not going to be upset over a $2.50 returned item. 
