---
layout: post
title: Utilitech - UTTNOREM2
date: 2024-02-18 08:00:00 -500
categories: [homelab, IOT]
tags: [iot, utilitech, rf, hackrf, urh] 
---

* Picked up a few of these outdoor wireless remote control outlets over the past few weeks. They are not bluetooth or wifi and seem to be operate like a garage door opener with and `on` and `off` button
# Device Info
* Model: UTTNOREM2 (0357410) Plug-in remote control
* FCC ID: QJXTNOREM02
* Model: UTTNOREM2 (T-001) Battery Powered Remote
* FCC ID: QJXT-001

## Acquisition
* I picked up 3x of these over the past few weeks from the local return auction. Two came with both the plug and transmitter but one only had the outlet plug

![d44981ecf66115654b5e6fc8de08e113.png](/assets/d44981ecf66115654b5e6fc8de08e113.png)

* Overall, spent only about $15.00 for the three of them which is  a good deal considering brand new they are around $12-15 each

## FCC lookup
* Loking up the FCC ID online
	* https://fcc.report/FCC-ID/QJXTNOREM02
	* https://fcc.report/FCC-ID/QJXT-001
## Receiver Internal Photos
* Internal photos from FCC database

![c85359f50f28df171830c2a0f59b1f7f.png](/assets/c85359f50f28df171830c2a0f59b1f7f.png)

* Do not see any modern style IOT chipset

![fa937b6d5e49dfe566c37006192c1a12.png](/assets/fa937b6d5e49dfe566c37006192c1a12.png)

* Out of the case

![4df8be64ffec3da3debda776b684d155.png](/assets/4df8be64ffec3da3debda776b684d155.png)

* Looks like this little PCB has the antenna and most likely A/D signal processing

![4101dfd3bc9fdc264ded1a3d966f2aa7.png](/assets/4101dfd3bc9fdc264ded1a3d966f2aa7.png)

## Transmitter
* No internal photos on FCC site
* Has frequency range on the FCC application

![7234323bc1884acd7af2ef1b4b73a7c2.png](/assets/7234323bc1884acd7af2ef1b4b73a7c2.png)

* Opened one of the transmitters up to take a look inside. Powered by a CR2032 3V battery

![4e9d72f129329e2b2a00bc1ec4cf8e80.png](/assets/4e9d72f129329e2b2a00bc1ec4cf8e80.png)

![2ca444632283ba65629bacc730769fee.png](/assets/2ca444632283ba65629bacc730769fee.png)


# Wireless Exploits
* Seems like a good target to try to capture the RF signal at 315MHZ and try to do a replay attack and also figure out the coding
* The transmitters are paired with the outlets by pressing both `on` and `off` simultaneously in the first 10-15 secs of the outlets being powered on
* I beleive this means the transmitter holds the magic and generates the unique parameters for the outlets to accept and then store in memory to listen for `on` and `off` triggers
* The outlets can be paired with both transmittters labeled as `0323` and `0623`
	* Assuming those numbers might come up during analysis of the signals and unique identifiers....

## HackRF One
* Using my Aliexpress knockoff HackRF One

![631f09538f70a521f05967a904434317.png](/assets/631f09538f70a521f05967a904434317.png)

## Universal Radio Hacker
* Download latest release of Universal Radio Hacker
	* https://github.com/jopohl/urh/releases/tag/v2.9.6
### Record Signal 
* Record the on and off signals from the 0323 transmitter

<video controls="" width="800" height="500" muted="">
<source src="https://www.jwowlearn2code.com/assets/0323TX_on_off_signal_record.mp4" type="video/mp4">
</video>


### Analysis - Interpretation Tab
* Interpretation tab shows the signal just captured
* Can leverage the `Autodetect parameters` functionality of URH and let it do some guessing for us
* Initial parameter guess assigned it as an `Amplitude Shift Key` signal with 1400 samples/symbol... which seems off
* Zooming in on one message block, there appears to be 25 messages that are repeated for both `on` and `off`

![379f329bc6911e9194997348d3080464.png](/assets/379f329bc6911e9194997348d3080464.png)

* Using some made up logic (maybe it's correct) and assuming some factor of `8` the Samples/Symbol is set to 200 (25x8) and the data viewed in hex cleans up nicely

![c6d2db7ec2d4017bdbe012eb3efe2c49.png](/assets/c6d2db7ec2d4017bdbe012eb3efe2c49.png)

* On Message >> `c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe300c0300c`
* Off Message >> `c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300c03f8c0300c` 
* Quick visual comparison and only 5 symbols are different between the two `fe300` and `c03f8` 
### Analysis - Analysis Tab
* Moving over to the Analysis Tab the differences are highlighted showing the two different hex pairs

![8a75e105fdb5e124302c1e5d2521e364.png](/assets/8a75e105fdb5e124302c1e5d2521e364.png)

* Clicking on the Analyze Protocol Button it creates 3 labels for synchronization, sequence number and length

![df0856baab46bca1ecf1aa0e0d89cad9.png](/assets/df0856baab46bca1ecf1aa0e0d89cad9.png)

* Not quite sure if this breakout and assignment is correct or actually means anything....
### Analysis - Generator Tab
* Dragging the on_off signal into the generator tab to try to create our own signal vs just replaying the captured signal


![9760f3ef0d73678828b11bbae484c9c7.png](/assets/9760f3ef0d73678828b11bbae484c9c7.png)


<video controls="" width="800" height="500" muted="">
<source src="https://www.jwowlearn2code.com/assets/0323TX_SignalGenerator.mp4" type="video/mp4">
</video>

* It successfully turns the outlet on and off

## Pair Signal
* Repeating the same steps as above to capture and analyze the pair signal for the `0323` transmitter
`insert video`


<video controls="" width="800" height="500" muted="">
<source src="https://www.jwowlearn2code.com/assets/0323TX_pairing_ananalysis.mp4" type="video/mp4">
</video>

## Breakout Analysis
* Trying to just visual and understand the differences...
* ON (0323)
	* `c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe300c0300c`
	* `fe300` unique symbols >> `fe` `00`
* OFF (0323)
	* `c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300c03f8c0300c`
	* `c03f8` unique symbols >> `c0` `f8`
* PAIR (0323)
	* `c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe3f8c0300c` 
	* `fe3f8` unique symbols >> `fe` `f8`

# Repeat with other transmitter
## Repeat with 0623 Transmitter
* Captured the on, off and pairing signals and walked through the same steps as above to break down to the anlaysis tab.
* Differences in analysis tab show entire sequence is different and not just the 4 hex values above. Samples/Symbol were also set to 300 vs 200 on the 0323 transmitter... think I'm definetly just making stuff up...
* ON (0623) 
	* `c01fc7f1fc7f1fc7f180c060301fc60301fc7f180fe3f8c07f180180c06`
* OFF (0623)
	* `c01fc7f1fc7f1fc7f180c060301fc60301fc7f180fe3f8c0603f8180c06`
* PAIR (0623)
	* `c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe301fc7f180c06`

### 0623 Table [on>off>pair]
```
c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe301fc6030180c
c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe30180fe30180c
c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe301fc7f180c06
```
* Last 10 hex values differ
### 0323 Table [on>off>pair]
```
c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe300c0300c
c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300c03f8c0300c
c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe3f8c0300c
```

### Both [0323 then 0623]
```
c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe300c0300c
c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300c03f8c0300c
c0300fe3f8c03f8fe300c0300fe300fe3f8fe300c03f8c0300fe3f8c0300c
c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe301fc6030180c
c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe30180fe30180c
c07f1fc7f1fc7f1fc6030180c07f180c07f1fc603f8fe301fc7f180c06
```
# Final Thoughts
* First time trying to walk through this with universal hacker radio.
* I have really no idea what I'm doing... but able to replay raw signals and generate signals based on the message breakdowns
* I don't understand what is going on inside the data itself and while there appear to be patterns they dont immediately pop out as an "aha that 0323 or 0623 transmitter!"
* More thoughts and analysis required for sure....

