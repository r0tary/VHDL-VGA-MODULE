# VHDL-VGA-MODULE
A VGA driver written in VHDL for displaying a 640x480 image on a VGA supported monitor, with an example drawing included.

## Project Description
[Design_TB_Sources]() contains the design and simulation sources for this project. [VGA-MODULE-proj]() contains the Vivado Project. While developing this project I used [VHDL Display Simulator](https://github.com/fcayci/vhdl-display-simulator) made by [fcayci](https://github.com/fcayci)

The module is split into 3 design sources: a 100MHz to 25Mhz clock divider done in [clock_div.vhd]()  generating the necessary timings like ***Hsync*** and ***Vsync***, done in [VGA_timing.vhd]() and generating the necessary output pattern to the display [pattern_generator.vhd](). Currently the module only supports the basic 640x480 resolution at 60Hz, which needs the 25MHz clock signal to drive it.

The hierchry for the design sources in Vivado:
```
+- Design Sources/          
| -- Video_Top.vhd           : Top File
  | -- clock_div.vhd         : Clock divider, from 100MHz to 25MHz
  | -- VGA_timing.vhd        : Timing genration
  | -- pattern_genrator.vhd  : Output image generation
+- Simulation Sources
| -- VGA_Module_tb.vhd       : Main simulation/testbench file, tests above 3 entitys
+- Constraints
| -- Zedbaord_master.xdc
```
--
## Simulation
[VGA_Module_tb]() has a slightly modified process used for writting RGB values into a text document, this is used by the [Display-Simulator]()

Below is a screenshot of a simulation depicting signals
![image](https://github.com/r0tary/VHDL-VGA-MODULE/assets/106680433/991c484a-68d0-4402-b3bd-a732edab9c3a)

Below is a screnshot of the current example object drawn and simulated by the [Display-Simulator.html]()

<p align="center">
  <img width="460" height="300" src="https://github.com/r0tary/VHDL-VGA-MODULE/assets/106680433/b888b2b9-9c47-40f5-9592-82489e506a64">
</p>

---

## VGA Architecture Description
<img align="left" width="400" height="250" src="https://i.imgur.com/bjhMcLc.png"> 

- **Image Generator:**  Produces pixel signals - R, G, B. They get converted to analog voltages between 0V and 0.7V by DACs *(usually with a resolution between 6 and 10 bits)*, before being sent to the monitor.

- **Control signal generator:** Produces the VGA clock ***clk_vga***, plus control signals ***Hactive*** *(horizontal active window)*, ***Vactive*** *(vertical active window)*, ***dena*** *(display enable)*, ***Hsync*** *(horizontal sync)* and ***Vsync*** *(Vertical sync)*. This block is application-independent *(it depends only on the VGA mode)*, so its design is always the same.

- ***DDC*** *(Display Data Channel)* - Allows the computer to read the display's features *(supported resolutions, timings etc.)*, stored in a ROM with extended display identification data (EDID) format. The original VGA mode (640 x 480 x 60Hz) is supported by any monitor by default. Employs I2C protocol. Is also application-independent.

#### VGA Signal Description 
- **Hsync** and **Vsync** - are responsible for determining when new line or a new frame should start, respectively, with their timings defining the VGA mode. 
- **Hactive** and **Vactive** - represent the time intervals during wich an image is actually being drawn on the screen. 
- **dena** *(display enable)* is responsible for tuning the pixel signals off during retrace, so it can simply be obtained by ANDing ***Hactive*** and ***Vactive***. 
 
*Note: Only 2 of the 5 signals are transmitted to the monitor.*


#### VGA Connector Pins
<img align="right" width="450" height="300" src="https://i.imgur.com/J6fSi4A.png">

There are 5 main signals sent to the monitor:
- pins **1 - 3** transmit the color signals - R, G, B. They are analog voltages between 0V and 0.7V on two parallel 75Ω resistors *(all other signals are digital)*.
- pins **13 and 14** transmit horizontal and vertical sync signals.

