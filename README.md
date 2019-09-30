# Modular Slam Robot

## Project summary

This project focuses on localization based experiments (e.g echolocation SLAM) and aims at providing a mobile modular robot. The robot and acts as a basis for various experiments with a shared mobile aspect. The modularity of the robot comes in two variants :  

  - Hardware : specific experimentation hardware can be mounted on top of the robot.
  - Software : the raspberry pi as well as power electronics ensure interoperability with most hardware.  

<p align="center">
  <img src="https://raw.githubusercontent.com/yahaggach/modular-slam-robot/master/readme-pics/architecture.PNG" width="350" title="Robot General Architecture">
</p>  

The robot was developped at EPFL (École Polytechnique Fédérale de Lausanne), under LCAV (Audiovisual Communications Laboratory) supervision, and tested on two separate experiments : EchoSLAM and Indoor Wifi localization.  


## Robot technical summary

<p align="center">
  <img src="https://raw.githubusercontent.com/yahaggach/modular-slam-robot/master/readme-pics/overview.PNG" width="350" title="Robot Overview">
</p>

### Mechanics

The robot's main mechanical characteristics are as follows :  

- **Differential drive** with two controlled wheels and a third free "caster ball" as support for the platform.
- **NEMA 17 stepper motors** coupled to both scooter wheels for high resolution and repeatability.
- **Laser-cut base** "parametric bolted" plate as main platform to allow mounting additional components.
- **3D printed mounts** and components for easy replacement and modification.  

All files to reproduce the robot can be found under `master/mechanical-files`. Fusion 360 files are available for modification, stl files are also available for printing with no modification. Please refer to `master/mechanical-files/drawing.pdf` for an idea about assembly. The remaining mechanical components : scooter wheels, nema 17 motors and caster ball are bought separatly and figure in the BOM `master/bill-of-materials`.  

**Any additional custom made part can still be designed, 3D-printed and assembled to the base plate using screws, nuts and one of the many holes available.**

### Electronics

A **breadboard** is used to make all the connections according to the schematics found in `master/electronic-schematics`, the breadboard then simply sticks to the base place. A **raspberry pi shield** is currently being designed and will contain all primary electronic components.  

The robot's main electronics are as follows : 

- **Raspberry pi 3 model B+** as computing unit, running the latest version of **OctoPi** (https://octoprint.org/download/).  
- **Arduino nano** running the latest version of **GRBL** (https://github.com/gnea/grbl/wiki), refer to the Frequently Asked Questions section for more details on compiling and flashing it to the Arduino Nano. Connects to the raspberry Pi using USB.  
- **TMC2208 silent stepstick** stepper drivers, connected to the arduino nano and to the stepper motors.
- **TTL Logic Level Converter** allows for communication between arduino nano and raspberry pi converts arduino's standard logic voltage of 3.3V to the raspberry pi's 5V.
- **TOF laser sensors** used to detect obstacle and measure distances.
- **18-21V rechargeable battery pack** custom made 5S2P battery pack using  10 x NCR18650 li-ion batteries with a BMS. This battery pack **can also be bought from retailers** (i.e https://voltaplex.com/5s2p-18v-6.7ah-li-ion-18650-battery-pack-panasonic-b-cuboid). Enclosure for the battery pack be designed 3D printed and mounted under the base plate between boths motors using a screw and nut. An exemple enclosure can be found under `mechanical-files/stl`, the battery pack is glued inside before assembly with base plate, a different battery pack might require a new design.
- **Step-down buck converter** to convert the voltage of the battery pack to the 5V needed to power all electronics.  

Additional electronic components may be added due to the strong interoperability of the raspberry pi. Mounts for electronic components can be designed , 3d printed and assembled by taking advantage of the many holes the plate has to offer. 

### Inverse Kinematics

The inverse kinematics of the robot are given by the following equations :  
  <br>
<p align="center">
  <img src="https://raw.githubusercontent.com/yahaggach/modular-slam-robot/master/readme-pics/differen.PNG" width="650" title="Inverse Kinematics">
  </p>  
  
### GRBL

Grbl ( is a free and open source software that allows control of motion machines. It has a G-code interpreter and generates the proper driving signals for stepper drivers. It fits perfectly on the Arduino Nano and connects to the raspberry pi (for g-code streaming) through serial USB. The pinout of the GRBL Arduino Nano is as follows :

<p align="center">
  <img src="https://raw.githubusercontent.com/yahaggach/modular-slam-robot/master/readme-pics/microcontroller.png" width="450" title="GRBL Arduino Nano Pinout"><br>
  <i>source : https://www.instructables.com/id/GRBL-Pinout-Arduino-Nano-v30/</i>
  </p>  
  
Pins **A0,A1,A2** can be connected to the raspberry pi's gpios (see schematics) through the TTL converter in order to stop and resume the motion through software or custom G-code command.  
GRBL needs to be configured to match with the robot's motion characteristics : **steps per mm**, **max acceleration**,**max speed...**.  
All **configuration parameters** can be found under : https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration.  
In addition, all **supported g-code commands** can be found under : https://github.com/gnea/grbl/wiki/Grbl-v1.1-Commands.
  




### OctoPi

OctoPi is an OctoPrint (https://github.com/foosel/OctoPrint/wiki) image for the raspberry Pi. It is a web interface primarily intended to control 3D printers, we can however repurpose it with GRBL and consider the right wheel as the X axis and left wheel as Y axis when streaming G-code.

<p align="center">
  <img src="https://raw.githubusercontent.com/yahaggach/modular-slam-robot/master/readme-pics/octoprint.png" width="550" title="Octoprint interface">
  </p>  
  
It is also extendable thanks to its support of user-made plugins. In order to be able to combine both GRBL and Octoprint, the following plugins must be installed through the plugin manager in Octoprint's settings :  

- **Better GRBL** (https://plugins.octoprint.org/plugins/bettergrblsupport/) allows to interface with GRBL powered microcontrollers.
- **GcodeSystemCommands** (https://plugins.octoprint.org/plugins/gcodesystemcommands/) allows to create custom G-code commands that can trigger linux or python scripts (i.e custom g-code command *OCTO 1* to turn on/off the GPIOs on the raspberry pi connected to the pins A0,A1,A2 of the Arduino Nano, see `master/electronic-schematics/ttl-raspberry.PNG` and schematics for more details)

