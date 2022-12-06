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

### Klipper installation

I am planning on using Klipper running on a RPi Zero V2. This was
my first Klipper install and so I mostly followed the videos
[provided by Rolohaun](https://www.youtube.com/watch?v=OmBIHB9TFgc&t=314s). 
Follow his instructions as provided. 


