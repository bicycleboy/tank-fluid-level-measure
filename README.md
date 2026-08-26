# Tank Fluid Level Measure

## Measure fluid level in a tank with a pressure sensor and Shelly Uni or Shelly Add On

## Overview

A project to measure fluid level in a tank suitable for rural water tanks, septic tanks etc. While there are many solutions to this problem, this project 
uses a pressure sensor and Shelly for reasons discussed below. It can be standalone using the Shelly app or integrated into home assistant for 
fancier displays and logic such as driving pumps.

The finished product and the home assistant dashboard:

<img src="images/septic-dashboard.png" alt="Home Assistant Dashboard" width="300"/>

## Features

- I believe this is more reliable and durable than alternatives
- Pretty easy (compared to say esp32/esphome/aduino style solutions)
- [Home Assistant](https://www.home-assistant.io/) integration
- Not so much a feature as a warning: this can be quite an expensive solution, north of AUD$100, but if you want to drive a pump, nothing like as expensive as a pump


## Hardware

- Pressure sensor such as [EARU](https://earu-electric.com) [0-10V pressure sensor](https://www.aliexpress.com/item/4000098291819.html)
- [Shelly Uni Plus](https://www.shelly.com/products/shelly-plus-uni) or [Shelly Add On](https://www.shelly.com/products/shelly-plus-add-on)
- Waterproof polycarbonate enclosure around 10cm x 8cm
- 12VDC power adapter
- Cable for 12v run between power supply and enclosure/Shelly. (For longer runs check the gauge is thick enough to avoid voltage drop) 
- 6 Wago 221 connectors or similar
- Barrel or similar connectors to attach power supply to 12v cable.
- 2 or 3 [cable glands/grommets](https://www.aliexpress.com/item/1005001999417645.html)
- Optionally waterproof DS18B20 temperature sensor

*Notes on hardware* 

When I first researched this project I saw a number of people had tried ultrasonic sensors and the like but I found [this video](https://www.youtube.com/watch?v=N90C9Xge8Z4) and went with it.
Lars' video has most of what you need to know, here I try to spell out the other stuff I learnt. 

A pressure sensor is expensive but basically just works while other methods don't seem to get rave reviews. 

The range of the sensor you need is the depth of your tank.  

The length of the cable is the depth of your tank plus the distance to the location of your Shelly. So you need to decide if you are going to get power to
your tank and your tank has wifi signal or if it is easier to run a longer sensor cable. For a price EARU will make cable lengths of 
25m, 50m (from memory maybe even 100m).  On Aliexpress you might have to message their support team to get a link for longer lengths. In most cases
running 12v power to the top of your tank is the obvious option. In my case for a septic tank buried in the ground there was no obvious place to secure 
the enclosure, wifi is weak, the enclosure might fry in the sun and running a decent gauge power cable to the tank had a cost too.   

The EARU brand sensors have a 0-10V output option and support DC10-30V input power (you need to select 0-10V when purchasing). Because of this I went with a Shelly Uni rather than a 5/3.3v esp32.  
I also figured 12v was better for longish distances.  I have no experience of other brand sensors but can update this if others have.

You can use a Shelly Uni Plus or if you want to drive a pump, a Shelly Plus Add On with Shelly Plus 1.  The advantage of the Plus versions is that they support a Voltmeter threshold 
which in effect cuts out the noise from the sensor which otherwise may change voltage constantly.

## Tools
- Screwdriver probably phillips to match polycarbonate enclosure
- Small flathead screwdriver for Shelly Uni
- A power drill and bits to make holes in the enclosure

##  Wiring diagram

![Schematic](/images/KiCad%20schematic.png)

## Software 


### Home Assitant Automations

If you choose to specify the deep sleep duration in you esphome yaml then you don't need these automations *at all*.  As I was learning and debugging I just found it handy to have logs and notifications of what was going on ie "why did my sensor die after only 2 days?"

Note these automations *rely on naming conventions* for your esphome device.  I started with "esp32-1, esp32-2..." for the friendly name and just ran with it.  You can use any convention you like but it can be handy to be consistent so you can use [templates](https://www.home-assistant.io/docs/configuration/templating/) such as `input_boolean.{{ esp_device }}_online` rather than many statements of almost identical code.

#### esp32-event-automation.yaml

When you first add deep sleep to an esp32 device you might notice that it goes unavailable.  The fix is to delete the device and re-add it, after which home assistant and any dashboards you have created will show the last known value.  The downside of this is that you sometimes want to know if the device is actually on.  You could use the last updated state in the dashboard for this but I ended up defining a toggle helper for each esp32 which can be displayed on the dashboard and trigger automations.  These helpers at least allow this automation to be simple and any automations for different types of sensors separate.  

#### soil-moisture-automation.yaml

As mentioned under *esp32-c6.yaml* above I found hardcoding deep_sleep duration unreliable and inflexible.  For another use case I wanted a different deep sleep pattern based on time of day and locations - probably not necessary for a soil moisture sensor but I suppose you might want it to wake before and after an automated watering then not for a day. 

The main reason sleep_duration_ms is calculated is so that I could take a reading once a day at about the same time and I might have recharged it during the day.  

An added benefit of setting sleep duration from home assistant is that when you are testing you can suspend or change the deep sleep - for example while you are making frequent over the air (ota) updates. In the testing scenario it is useful to have a boot/sleep notification so you don't confuse yourself with home assistant sleeping the esp32 when you did not expect.

## Step by Step Instructions

Purchase the hardware, noting points above on choosing cable lengths.
1. Power up the shelly and connect to your wifi, update firmware etc.  At this stage you can power the Shelly 

## Troubleshooting

### Hardware


### Home Assistant integration issues


## License

This project is free to use under the [MIT License](https://github.com/bicycleboy/tank-fluid-level-measure?tab=MIT-1-ov-file).
