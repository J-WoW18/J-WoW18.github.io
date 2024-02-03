---
layout: post
title: Enbrighten - WFD4203E
date: 2024-02-03 12:00:00 -500
categories: [homelab, IOT]
tags: [bk7321n, iot, enbrighten, wifi, bt] 
---
* Dual outdoor smart switch by Enbrighten picked up at the local auction return this week. 
# Device Info
* Model: WFD4203E
* FCC ID: QOB-WFD4203
## Acquisition
* Another cheap win at only $3. These go for $10-20 new.

![c4cbb3b774fd6ec5844cfec620510bc4.png](/assets/c4cbb3b774fd6ec5844cfec620510bc4.png)

## FCC lookup
* Loking up the FCC ID online
	* https://fcc.report/FCC-ID/QOB-WFD4203 
* User manual available
  
![fb9cd8bef1a14087420d030119d60ffc.png](/assets/fb9cd8bef1a14087420d030119d60ffc.png)

* Internal photos in the database show a CB2S module attached to the main PCB.
  
![a98d66d48703331f4dce49ac9044e5b4.png](/assets/a98d66d48703331f4dce49ac9044e5b4.png)

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
  
![9998d57df6d15a02be53c27dbfea2246.png](/assets/9998d57df6d15a02be53c27dbfea2246.png)

![b7ce6b147b92cbaf9fcf6d267a38c2ba.png](/assets/b7ce6b147b92cbaf9fcf6d267a38c2ba.png)

# Teardown & Connections
## External Photos
* Packaging opened and returned.
  
![6e293cc9d36888b5854b9625b876a16e.png](/assets/6e293cc9d36888b5854b9625b876a16e.png)

* Top of device
  
![b6b2ccc29d185b3486920be04c1dfe86.png](/assets/b6b2ccc29d185b3486920be04c1dfe86.png)

* Bottom of device with label
  
![0f88e620bd9248a70bcc5fcd678cfa64.png](assets/0f88e620bd9248a70bcc5fcd678cfa64.png)

## Internal Photos
* There are four triangle screws holding the back cover on. Once open, the PCB with two relays and the CB2S module are exposed.
  
![e74848274cf85edaa51a4d84227c1dd4.png](/assets/e74848274cf85edaa51a4d84227c1dd4.png)

* Orientation of the CB2S module shows 3.3v location towards the top and outside of the PCB.
  
![b4b67b6cd17490476b5933ec6f870a6b.png](/assets/b4b67b6cd17490476b5933ec6f870a6b.png)

* In the PCB holder and IDing 3.3V, GND, RX, TX pin that need to be connected to the serial to usb device
  
![d3039ab13d8e9084fd67ef3c672bdfae.png](/assets/d3039ab13d8e9084fd67ef3c672bdfae.png)

* Pins connected
  
![154bac1fda380fedce2a1c4dbe4e6f06.png](/assets/154bac1fda380fedce2a1c4dbe4e6f06.png)

* Powered on and connected to computer
  
![e301ae035315991888ba45bfdeda5409.png](/assets/e301ae035315991888ba45bfdeda5409.png)

# Firmware Dump
* Serial to USB converter connected up to computer and ltchiptool was used to interact with the CB2S module.
	* https://github.com/libretiny-eu/ltchiptool

## Chip Info
* Chip info showing the BK7231N chip
  
![3bef2e6852bb497a972f3d5342affa74.png](/assets/3bef2e6852bb497a972f3d5342affa74.png)

## Read Flash
* Firmware saved and re-read in LTChiptool to figure out pin assigments and configurations
  
![2024-02-02 18_26_17-enbright_dual_outdoor_1.png](/assets/2024-02-02%2018_26_17-enbright_dual_outdoor_1.png)

# ESPHome
## ESPHome Config

```YML
esphome:
  name: enbrighten-dual-1
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
  fast_connect: true
  manual_ip:
    static_ip: 192.168.20.93
    gateway: 192.168.20.1
    subnet: 255.255.255.0
text_sensor:
  - platform: libretiny
    version:
      name: LibreTiny Version
switch:
  - platform: gpio
    id: switch_1
    name: Relay 1
    pin: P26
    on_turn_on:
      - light.turn_on: light_status
    on_turn_off:
      - light.turn_off: light_status
  - platform: gpio
    id: switch_2
    name: Relay 2
    pin: P24
    on_turn_on:
      - light.turn_on: light_status
    on_turn_off:
      - light.turn_off: light_status
binary_sensor:
  - platform: gpio
    id: binary_switch_all
    pin:
      number: P8
      inverted: true
      mode: INPUT_PULLUP
    on_press:
      then:
        - switch.toggle: switch_1
        - switch.toggle: switch_2
light:
  - platform: status_led
    id: light_status
    pin:
      number: P7
      inverted: true
```

* In Home Assistant and ESPHome, a new device is created matching the device name in the YAML. Device can be installed, downloaded and flashed using LTChiptool.
  
## ESPHome Operation
* After flashing and power cycling the device. Pins were unhooked and the 120V outlet is plugged in. After a few seconds the device joined
* ESPHome dashboard showin operation and individual relay operation
  
![0ea7460fc8937ee9ed808556a8ee0368.png](/assets/0ea7460fc8937ee9ed808556a8ee0368.png)

* Webserver view showing the same individual control of the relays for each outlet
  
![7c23c5cb067349472d88db7367eeb414.png](/assets/7c23c5cb067349472d88db7367eeb414.png)

# Final Thoughts
* The device has two 120v outlets that are controlled independantly. This opens up more fine tuning for automations and control for two different devices. With the BK7321N chip on the CB2S the ESPHome YAML can be updated to also add BLE functionality and turn the outdoor device into a BLE relay.
