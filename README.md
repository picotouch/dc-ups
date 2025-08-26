# DC DIY UPS - DCzilla


## Introducion
I’ve been working on a DC DIY UPS solution for a while because I couldn’t find a device on the market that does exactly what I need: multiple various DC outputs, user configurable and without multiple voltage conversion (AC→DC, DC→AC, AC→DC).

This DC DIY UPS can output different voltages at the same time (for example, 5V for a Raspberry Pi, 12V for a switch, and 15V/19V for a router or mini-PC). 

I intend to use it to power the Dell Optiplex Micro/similar or SBC with Home Assistant, router, and switch, to ensure uninterrupted power to the entire network.
<br/><br/> 
  

## Features:
- Up to 3 outputs, each with its own voltage, 5 / 9 / 12 / 15 / 19 V (same or different per output)
- User-defined battery high/low thresholds for automatic output on/off
- User-defined power-up and power-down delays for safe shutdown
- Status message outputs for command execution (COM port) via Node-RED
- Battery voltage monitoring
- Modular design using pre-assembled DC-DC buck/boost converters and a battery charging module
- DC input via standard 19–20 V AC/DC supply (e.g., notebook adapter)
- 12 V lead-acid batteries (7–9 Ah), as in classic UPS systems
- Fanless operation      
- Compact dimensions
<br/><br/> 


## Block diagram:
<br/><br/> 
<img width="75%" height="auto" alt="block_diagram" src="https://github.com/user-attachments/assets/7657dd56-ba92-4272-aa33-60609256cb3b" />
<br/><br/>



## System components:
-  main control PCB (DIYUPS3)
-  DC/DC buck-boost converter, ( pre-assembled module), up to 3
-  battery charger module, ( pre-assembled module)
-  lead acid battery(SLA/VRLA)
-  metal enclosure
-  3D printed front/rear panel
-  3D printed PCBs holders
-  connectors for DC inputs/outputs, communication/configuration(COM port)
-  AC/DC power supply (e.g. Notebook power adapter)
<br/><br/> 


## Description of system components:

###  -  DIYUPS3
This is the main PCB that accepts external power supply (DC) via AC/DC power supply or battery and provides power to the DC/DC converter. 
The “Ideal diode controller” with the corresponding MOSFET (so-called Active OR'ing Redundant Power Supplies) is responsible for this.

DC/DC converters ensure output DC for devices.
 
The PCB contains screw terminals for the DC input voltage as well as the battery, and fuses for both.
There are also fuses on the PCB for the DC/DC outputs. All fuses are Auto Mini Blade type.

The connectors for connecting the DC/DC converter modules are Spring PCB Terminal Block Wire Connectors.

There is also a PIN header connector on the PCB, on which there are pins for device communication/configuration (USB), and pins for DC/DC converter control (ON/OFF).

Management/control/monitoring goes through the Attiny1614 micro controller.



###  -  Battery charger module
For battery charging, a pre-assembled charging module (SDLA12TA) is used. It is recommended to replace the original Rcs resistor (0.4 Ω) with a suitable value to achieve the optimal charging current, which should be 1/10 of the battery capacity. This module is soldered directly onto the DIYUPS3 PCB. 


###  -  DC/DC buck-boost converters

Pre-assembled modules featuring the popular LTC3780 chip (Analog Devices, LTC3780 Rev G) were used as the DC/DC converter.
After testing multiple modules, this one proved to be the best choice for this application. While some criticisms of this module can be found online, in my experience most issues are due to component defects (e.g., low-quality voltage regulation potentiometers) or improper handling (such as touching components in the voltage regulation circuit).
Since this module includes some functions that are unnecessary for this application, minimal modifications are recommended.

The modifications are as follows:

-  removal of the CC function (constant current control): This function is unnecessary and can be too fast or sensitive for demanding loads. Over-current protection may trigger when a connected device, such as an HDD, draws a higher current at startup.

It is important to note that removing the CC function does not disable over-current protection. A short circuit at the output will still blow the fuse, protecting the module from damage.


-  removal of the UVLO function (under-voltage lockout): This function prevents excessive battery discharge when running on battery. Since the same function is implemented in the ATtiny microcontroller (and is user-programmable), the module’s UVLO is not required.
To use UVLO via the microcontroller, solder a 1-pin header (M) to the pin behind the IN connector and connect it to the corresponding header on the DIYUPS3 using a jumper wire (F–F).

Both the CC and UVLO functions are implemented on the same chip, which simplifies the modifications.


 -  voltage adjustment (CV): The output is controlled by a multi-turn potentiometer, which is recommended to be replaced with a fixed resistor. The potentiometer can cause voltage fluctuations due to its high resistance (500 kΩ) being sensitive to touch, or due to contact loss in low-quality units. Replacing it with a fixed resistor solves both issues. Use a 1206 SMD resistor for R-up and an 0805 for R-down.      

The formula for calculating resistors for different output voltages is:

Vout=0,8V*(1+Rup/Rdown)

Values for various voltages are listed in the table:

| Vout | Original R-down | Original R-up | Changed R-down | Changed R-up | Changed R-down | Changed R-up |
|  :----: |  :-------: |  :-------: |  :-------: |  :-------: |  :-------: |  :-------: |
| 5 V   | 14K            | 75K           | 10K                    | 53.6K               | 6.8K                   | 35.7K               |
| 9 V  | 14K            | 123K          | 10K                    | 102K                | 6.8K                   | 98.8K               |
| 12 V  | 14K            | 196K          | 10K                    | 140K                | 6.8K                   | 95.3K               |
| 15 V | 14K            | 249K          | 10K                    | 178K                | 6.8K                   | 121K                |
| 19 V | 14K            | 328K          | 10K                    | 234K                | 6.8K                   | 158K                |


Do not use R-down lower then 6.8K.  

These changes (CC and CV) are not necessary, everything works without these modifications, but they are recommended.


###  -  lead acid battery
The battery is a 12 V/9 Ah, commonly used in UPS systems and widely available in stores. 
A 7 Ah battery of the same dimensions can also be used, but given the small price difference, the 9 Ah option is preferable.

It is important to ensure the correct battery charging current by adjusting Rcs. 
Rcs is SMD size 1210.

Rcs values for various charging currents are provided in the table:

| Battery Capacity | Charging Current | Rcs   |
| :-------: | :-------: | :-------: |
| 1.2 Ah          | 120 mA         | 1 Ω   |
| 2 Ah            | 200 mA         | 0.6 Ω |
| 2.7 Ah          | 270 mA         | 0.444 Ω |
| 3.4 Ah          | 340 mA         | 0.3 |
| 4.5 Ah          | 450 mA         | 0.266 Ω |
| 7.2 Ah          | 720 mA         | 0.166 Ω |
| 9 Ah            | 900 mA         | 0.133 Ω |


###  -  metal enclosure
      
The enclosure is made of 1.5 mm thick galvanized sheet metal and consists of two identical U-shaped sections. It features openings for air circulation as well as holes for mounting brackets for PCBs, etc.
The width matches that of a Dell Optiplex Micro (182 mm). 
Sketches will be attached later.
  
###  -  3D-printed front/rear panels
The panels are 3D printed with a total thickness of 7 mm (5 mm outside + 2 mm inside the enclosure). 
The front panel features only the logo, while the rear panel includes holes for connectors. 
Sketches will be attached later.

###  -  3D-printed PCB holders
All brackets are 3D printed and equipped with M3/M4 threaded inserts.
Sketches will be attached later.

###  -  Connectors for inputs/outputs and communication/configuration:
-  Input DC voltage: XT60 (male), 1 pc
-  Output voltages (DC1, DC2, DC3): GX16 4‑pin connector. Each voltage uses separate pins, so accidental swapping of DC ports will not damage the system.
-  Communication/configuration: USB type A (female), 1 pc
          
-  External AC/DC power supply (e.g., notebook adapter)
A notebook adapter can be used as the input. The required power depends on the load, so select a supply that provides adequate capacity.

DCzilla is designed for a total power of approximately 100 W, sufficient to power a mini PC (e.g., Dell Optiplex Micro, 10–50 W), a router (15–25 W), 
and an access point or switch (15–25 W), with a combined maximum of around 100 W.
Always choose a power supply rated above the expected load. The output connector should be replaced with an XT60 (male) for compatibility.


