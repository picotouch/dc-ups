# DC DIY UPS - DCzilla


## Introducion
I've been working on a DC DIY UPS solution for some time now because I couldn't find a device on the market that does exactly what I need: multiple different DC outputs, user configured settings, no multiple voltage conversion (AC→DC, DC→AC, AC→DC), compact size, etc...

This DC DIY UPS can simultaneously provide a different voltage on each of the outputs (for example, 5V for Raspberry Pi, 12V for switch and 15V/19V for router or mini-PC).

I plan to use it to power a Dell Optiplex Micro/similar or SBC with Home Assistant, router and switch, to provide uninterrupted power to the entire  system.
<br/><br/> 
  

## Features:
- Up to 3 outputs, each with its own voltage, 5 / 9 / 12 / 15 / 19 V (same or different per output)
- User-defined battery high/low thresholds for automatic output on/off
- User-defined power-up and power-down delays for safe shutdown
- Status message outputs for command execution (COM port) via Node-RED
- Battery voltage monitoring
- Modular design using pre-made DC-DC buck/boost converters and a battery charging module
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
- main control PCB (DIYUPS3)
- DC/DC buck-boost converter, (pre-made module), up to 3
- battery charger module, (pre-made module)
- SLA/VRLA battery
- metal enclosure
- 3D printed front/rear panel
- 3D printed PCBs holders
- connectors for DC inputs/outputs, communication/configuration(COM port)
- AC/DC power supply (e.g. Notebook power adapter) 
