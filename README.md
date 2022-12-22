# Rook

This is my fork of the GitHub repo for the [Rook](https://github.com/rolohaun/Rook), a mostly 3D printed CoreXY 3D printer designed
by rolohuan. 

![](Build_Photos/rook.png)

Please see his repo for offical build files and other information. I will be using this repository to
log my build of the printer and store any additional / useful files so that they
can be discovered by others who might want to build this printer.

## Part ordering

Went through the Rook [BOM](https://docs.google.com/spreadsheets/d/1oHDEvndkkvPFOBis4atrHRHK_DMTvttFUFWDg2He6To/edit#gid=0) and
ordered everything specified pretty much using all the provided AliExpress links. 

After I made my order I and looking at the CAD files more closely I realized there was no
hardware specified for attaching the Z axis lead screws to the stepper motors. So I
ended up ordering [this flexible couplings from amazon](https://www.amazon.com/AFUNTA-Flexible-Couplings-Compatible-Machines/dp/B07JL1QYLS/ref=sr_1_4?crid=4MQ82V5BYPGQ&keywords=flexible+couplings+5mm+to+8mm&qid=1670003750&s=industrial&sprefix=Flexible+Couplings+%2Cindustrial%2C145&sr=1-4).
In the future I'm guessing equivalent AliExpress couplings will be added to the BOM.

## Software

### Klipper

#### Raspberry Pi

The Rpi hosts the Klipper web interface and API.
I am planning on using Klipper running on a RPi Zero V2. I followed the installation
guide [provided by Rolohaun](https://www.youtube.com/watch?v=OmBIHB9TFgc&t=314s)
pretty much to the letter and got my MainSail interface running without
a problem

#### Klipper and MainSail installation

Follow [part II of Rolohuan's Klipper installation guide](https://www.youtube.com/watch?v=-SYqTW7wJTo). The firmware
build will be specific to the mainboard that you have selected. I have decided to use the Makerbase
MKS Robin E3 V1.1 mainboard. 

#### Configuration file creation**

Each printer will require a configuration file that is specific to both the printer itself and the mainboard
being used to control it. This file communicates which pins on the mainboard does what to
the computer that is actually controlling the motion of the printer. It also
communicates the "species" of motion system (cartesian, corexy, delta) as the same inputs would
provide very different results if sent to each of these different systems.

In order to create this file for the Rook + MKS Robin E3 V1.1 I will be using I started by opening
both the configuration file provided by Rolohaun for the [Rook + SKR Mini E3 v3](https://github.com/rolohaun/Rook/blob/main/Klipper%20Config/SKR%20Mini%20E3%20v3%20Config/printer.cfg) and the configuration file provided from the Klipper GitHub for
the generic [MKS Robin E3 mainboard](https://github.com/Klipper3d/klipper/blob/master/config/generic-mks-robin-e3.cfg) and started
mashing them together. Basically this meant opening both files in side-by-side windows in VS code, identifying
the differences between the files that **did not** relate to the mainboard pinout and manually merging
into one hybrid document that has the pinout specific to the MKS Robin E3 mainboard but the motion system
parameters for the Rook.

Ultimately that process resulted in [this configuration file](https://github.com/EthanHolleman/Rook-build/blob/main/Klipper%20Config/MKRobinE3V1.1/rook/rook-mks-robin-e3.cfg)

#### Local acccess point

Part of the appeal of the Rook to me is its small size which allows it to be extremely portable.
One challenge I anticipate of bringing this thing around to new places is that
there is no physical interface for controling the machine; everything is depedent on being
able to access the Klipper Mainsail interface. This means that the machine needs to be
connected to wifi so I can use a labtop on the same network 
or other device to access the Mainsail interface. This would be problematic in locations
where there is poor or no access to a local network. 

To get around this I am using a Raspberry Pi Pico W as a local access point. Following
[this guide](https://www.recantha.co.uk/blog/?p=21398) from Michael Horne I got my
Pico hosting a local wifi network. Then when I need to connect to the Mainsail interface
and / or work on the Raspberry Pi Zero running Klipper for the Rook I connect
to this local network from my labtop. 

Code I am using for the access point is below.

```python
import socket
import network
from machine import Pin
import time

ssid = "YOUR SSID"
password = "YOUR PASSWORD"
led = Pin("LED", Pin.OUT)

ap = network.WLAN(network.AP_IF)
ap.config(essid=ssid, password=password) 
ap.active(True)

print("Access point active")
print(ap.ifconfig())

while True:
    if ap.active() == True:
        led.on()
    else:
        led.off()
        
    time.sleep(0.1)
```

- TODO: Create mount for Pico that attaches to printer frame and find a 5V pin
on the mainboard that can be used to power the Pico.



