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

Follow [part II of Rolohuan's Klipper installation guide](https://www.youtube.com/watch?v=-SYqTW7wJTo). The firmware build will be specific to the mainboard that you have selected. I have decided to
use the SKR E3 V3 because this is the board used by Rolohaun in his build. 

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

I then setup my RPi Zero that is actually running klipper to connect to this network when it boots up.
You can use [this guide](https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html) to
configure headlesss SSH access.


### Putting things together

First I started by printed out the main components of the frame using Fusion Filaments PLA Pro +.
The Rook is very sensitive to warped parts so I set the bed temp extra high, used a brim
when printing large frame parts and printed the first layer very slow and very hot.

![](images/IMG_2728.jpg)

With the main frame parts finished I tested the
fit of the linear rods and made sure the delrin
bearings were sliding smoothly along them.

![](images/ezgif-1-534774e55d.gif)

Next I started fitting the rest of the frame and
bed together as well as setting the M3 heat
set inserts using my soldering iron. Everything
went together pretty easily, just make sure all
parts are oriented correctly. I assembled things
backwards at some point. It helps to have the CAD
model open when first putting the frame together
to keep yourself on track as the frame is pretty
symmetrical. 

![](images/IMG_2732.jpg)

I later ended up replacing the bed shown in this
image with the two bearing bedframe that can
be downloaded from Rolohaun's Rook repo. I
found that this bed really does help to reduce
binding on the Z axis. The Rook seems to be very
sensitive to the linear rods not being perfectly
square. The degree of squareness required for a
smooth four bearing set up seems to be difficult
to achieve with 3D printed parts. 

I also lubricated all my linear rods with
Superlube grease by just placing some onto
the rails, moving the rail carriage through the grease
and wiping up the excess. This seemed to work well enough.

![](images/ezgif-1-2e3a1e77f5.gif)

Next I printed and assembled the idler towers.
These were a bit tricky to fit everything together.
It helped to sand down the inside area where the washers make contact with the printed plastic with a file. I also used the M5 bolt as a guide to kind of pile the bearings and washers on. As I added more I would screw in the bolt to hold everything in place.

![](images/IMG_2740.jpg)

With these parts together I then started working on the hot end assembly and the belting. 

![](images/IMG_2754.jpg)

I found this image of a general coreXY belting diagram to be very helpful.

![](https://i0.wp.com/3ddistributed.com/wp-content/uploads/2020/04/CoreXY-Kinematics.png?resize=768%2C610&ssl=1)

After I had the belting figured out I started mounting the bed. I used a 150 mm piece of glass pane I had cut at
a local hardware store with a PEI magnetic build plate as the print surface. 

![](images/IMG_2856.jpg)

I also designed some bed spring tensioning knobs that use the same threaded inserts as the rest of the build.
Double sided tape did not seem like enough to keep the bed attached to the mounts so I just superglued
these to the glass plate.

I then realized that the 40 mm cooling fans I had were too large to fit the hot end mount. So I went into Fusion360 and designed a
mount that would work with my hot end (CR-10 style) and a 40 mm fan. You can download the stl [at this link](https://github.com/EthanHolleman/Rook-build/blob/main/ETH-STLs/X_Carriage-40mm-extra-tall.stl).

Next I moved onto the wiring. I did not want to design a mount for all my individual components so instead I designed an electronics
box that slides into the bottom frame that you can just shove all the wires and computers into and then seal up and never think
about again.

Something to note about this electronics box
is that it needs to be installed *before* you
mount the Z axis motors and the legs because
it will be obstructed and difficult / impossible
to push up from the bottom of the frame otherwise.

![](images/IMG_2851.jpg)

Wires for the Z axis motors can be routed
through the holes adjacent to them. 

Everything fits I promise!

![](images/IMG_2863.jpg)

After verifying that everything seemed to be working I wired in the cooling fan, did some additional cable management and then sealed everything up using the lid I designed for the electronics box. I also threw in another 40 mm
fan into the enclosure in an attempt to help 
keep things a bit cooler in there. The links for both the box and lid are listed below.

- [Electronics box stl](https://github.com/EthanHolleman/Rook-build/blob/main/ETH-STLs/electronicsBox.stl)
- [Electronics box lid stl](https://github.com/EthanHolleman/Rook-build/blob/main/ETH-STLs/electronicsBoxLid.stl)

![](images/IMG_2865.jpg)

The only thing left to do at that point was to mount the part cooling fan and test everything out! After leveling the bed and fooling around
with the Z offset in Klipper I was able to print a calibration cube.

And with that my Rook build was complete. I really enjoyed putting this machine together and feel like I have a much deeper understanding of
3D printers and the mechanics that allow them to function.


[Video of first print](https://www.youtube.com/watch?v=ggh4lNVPjAo)

I posted my Rook to Rolohan's discord server and officially received serial number 27! So this is the 28th Rook to exist in the world (counting starts at 0). Overall this was a very fun project and I would
recommend Rook to anyone looking to try out building
a printer from scratch but does not want to shell
out the cash for a Voron type build. The mostly
3D printed design and relatively low cost to build
(mine came in at around 300 USD when all was said
and done but that could definitely be powered due to
some purchasing inefficiencies on my end) make this
build low risk. An added bonus is that the small
size and light weight means that you can fit
this guy into your second carry on bag no problem.
I fly home for Christmas with mine without any
issue!






