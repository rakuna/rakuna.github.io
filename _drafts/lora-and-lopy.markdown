---
layout: post
title: LoRa and LoPy
---

So I've decided to look into the LoRa networking system via the LoPy hardware.

...This was paid using hackathon winnings.

I intend to use the hardware to:

- test LoRa networking.
- Attempt to make a WiFi bridge over the LoRa network
- communicate between my car and home and test the potential range of LoRa
- understand the regulations related to LoRa usage
- Stretch goal &nbsp; communicate between my home and office using LoRa (either my own LoRa node, or company's LoRa node) &nbsp;

Getting started

1. Unboxing / the hardware [Pictures]
2. Getting started (Documentation) The packaging references the getting started at [www.pycom.io/gettingstarted](http://www.pycom.io/gettingstarted), which times out. Instead it looks like pycom documentation is available at [https://docs.pycom.io/chapter/gettingstarted/](https://docs.pycom.io/chapter/gettingstarted/). (I informed pycom of this, and they setup a redirect from the first to the second).
3. After finding the documentation, it is time to plug the LoPy into the expansion board (being mindful) of orientation (I assume the device might be damaged by putting Vin and GND to the wrong pins if plugged in incorrectly) Once the LoPy is plugged into the expansion board, plugging into a micro USB port produces a pulsing blue LED heart-beat on the LoPy.
4. .
5. With the latest update to Atom (v1.9.0), the Pymakr plugin broke, which was disappointing...

References:  
MQTT information [http://www.hivemq.com/blog/mqtt-essentials/](http://www.hivemq.com/blog/mqtt-essentials/)  
MQTT Broker [https://io.adafruit.com/](https://io.adafruit.com/)

