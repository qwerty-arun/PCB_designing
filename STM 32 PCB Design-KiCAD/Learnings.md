# [YT Tutorial](https://www.youtube.com/watch?v=aVUqaB0IMh4&list=PLXSyc11qLa1b9VA7nw8-DiLRXVhZ2iUN2&index=5)
## What?
- STM-32 Blue Pill, we will be using. STM32F103C8T6
- VSS is the ground supply for the digital side and VSSA is ground reference for the analog side.
- VBAT is for battery, we just connected it with VDD for now.
- Decoupling capacitors will be place very near to integrated chips and will act as small energy storages. This will help in quick switching transients.
- One capacitor per VDD pin and VBAT pinis necessary.
- We also place a bolt decoupling capacitor.
- `What is a decoupling capacitor?` In electronics, a decoupling capacitor is a capacitor used to decouple (i.e. prevent electrical energy from transferring to) one part of a circuit from another. Noise caused by other circuit elements is shunted through the capacitor, reducing its effect on the rest of the circuit. For higher frequencies, an alternative name is bypass capacitor as it is used to bypass the power supply or other high-impedance component of a circuit.
- `What is a ferrite bead?` A ferrite bead is a passive device that filters high frequency noise energy over a broad frequency range.
- Analog is more sensitive, that's why it requires more filtering which is more common in STM32 designs.
- Ferrite beads are typically given a resistance at 100MHz, which in this case is 120 ohms at 100MHz.
- [Blue Pill](https://stm32-base.org/boards/STM32F103C8T6-Blue-Pill.html)
- [Datasheet](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf)
- If 75MHz is the maximum frequency the microcontroller can handle, then why are we using a ferrite bead?
- NRST: negate reset pin, if low, it will put the microcontroller in reset mode, if high, makes sure the microcontroller runs the software that's flashed to it. It contains an internal pull up resistor, so we can leave it floating. We can add an external switch if we want to manually control.
- If you don't want to use the NRST pin, use a 100nF capacitor (decouping), to prevent spurious resets.
- BOOT0: enables or disables the internal bootloader of the STM32 board.
- ST-Link Debugger: Main way to program the Microcontroller. Connect one side to your host computer via USB and other side via serial wire debug to the microcontroller.
- This case requires us to use the USB of the chip. Making the BOOT0 pin high enables interfaces such as UART or USB or I2C. Because we want to flash programs via USB to this device.
- We just use a switch: SPDT, single pole dual throw switch.
- We can use `x` or `y` keys to flip symbols.
- [STM32IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- Done Downloading STM32IDE
## STM32IDE Software
- Start a new STM32 Project
- Select the MCU/MPU Selector. Just select the part no which in our case is STM32F103C8. It provides a description of the board itself which is helpful when we are selecting boards.
- Add a new name for the project, it shows the pinout diagram. You can configure pins as you like.
- You can refer to datasheets, but at this stage, not adviced.
- Go to the left side on categories tab: System Core-> SYS->Debug->Serial Wire.
- This enables PA13: Serial wire input and output pin as well as PA14: Serial Wire Clock pin
- Trace Asynchronous is used for monitoring live variables.
- RCC: Select the high speed clock which aids in timing when we use uart.
- Connectivity-> USB. This enables PA12 and PA11 which is the USB differential pair.
- MiddleWare-> USB
- Clock Config: You can input the crystal frequency.
- PD0 and PD1 are pins for the oscillator.
- After configuration in IDE, transfer the pins on to KiCAD
## Crystal Circuitry
- KiCAD automatically detects the USB differential pair only if the pins end with `+` and `-` and also the names should be different before that.
- Crystals typically come in 4 pins: we choose crystal_gnd24
- A very important datasheet: Application note of [AN2867](https://www.st.com/resource/en/application_note/an2867-guidelines-for-oscillator-design-on-stm8afals-and-stm32-mcusmpus-stmicroelectronics.pdf) which is an oscillator design guide for STM32.
- Page 11: Oscillatory circuitry.
- The load capacitors will have same values and see the recommended values in datasheet
- In the clock config of IDE, the max frequency was 16MHz, so the cap's value=10pF
- Calculation of load capacitance: 10pF-5pF and then multiply by 2 which is again 10pF. Here, 5pF is the stray capacitance.
## USB
- We will be using USB-B-Micro
- Pressing `q` will bring no connection flag
- Shield will not be connected
- ID pin is typically used when we are switching between host and device modes
- Application note of [AN4879](https://www.st.com/resource/en/application_note/an4879-introduction-to-usb-hardware-and-pcb-guidelines-using-stm32-mcus-stmicroelectronics.pdf)
- We need a pull up resistor of 1.5k Ohms
- Don't type in 1.5k, type `1k5` so that it's easier to read
## Power supply and connections
### Serial Wire Debug
- We use 1 column and 4 row pin
- Pin 1 is connected to 3.3V
- Pin 4 to gnd
- Pin 2 to Serial wire Debug IO pin
- Pin 3 to Clock
- We will be using a linear regulator, not a switching one because this board is low power and we want something that just works. So AMS 1117-3.3
- Linear regulators always require input and output capacitors for stability. [Datasheet](https://www.alldatasheet.com/datasheet-pdf/view/49118/ADMOS/AMS1117.html)
- These capacitors are called bypass capacitors or decoupling capacitors
- VBUS coming from the USB are actually very noisy, so we need additional filtering, the current capacitor will do nothing. But for simplicity, we will add nothing
- When working with power rails, make sure you add LEDs and a limiting resitor.
- Since we are already using 1.5k resitor, to reduce the BOM, use this itself.
- Go back to STM32IDE, hover over the USART pin, enable it, it seems like it is too close to the USB pins, so Ctrl click them to see where else you can place them.
- Configured I2C2 too as I2C1 was close to USART1
- Rest of the pins have the no connect flag on them
- We will be using the same pin connectors which we used for USB for USART and I2C.
- USART is fine, but for I2C, they need pull up resistors.
- Typically for I2C at high speed like 400kHz, we use 2.2kHz but since we are using 1.5kHz, we will just go with that.
## Annotations
- Add a rectangular block for both the linear regulator circuitry and microcontroller circuitry.
- Fill the Title block by clicking on page settings.
- Go to annotate schematic option and reset the current annotations, then click on annotate.
- This depends on alignment of the parts, so you might wanna do it manually.
- We need to add footprints now.
## Footprints
- Symbols are the entities that live on the schematics and footprints are the entities that correspond to the symbols that live on the PCB.
- Before that we have to check electrical rules check.
- We got one error regarding the +3.3VA flag. KiCAD doesn't know whether it is a input or an output, thus the error. To get rid of it, we use a power flag.
## Assigning Footprints
- We can create our own or just download from the internet.
- For bigger SMD components select larger size footprints. For now since capacitor's value is small, stick with 0402 and 0603.
## PCB Set-up
- For our board, two layers are sufficient.
- Go to board set up option on the top left.
- For a two layer board, typically you use the top layer for signal and bottom layer for ground plane.
- Our typical board thickness will be 1.6mm
- In constraints section, choose your manufacturer and add the capabilities.
- For pre-defined sizes, track widths: 0.3 mm and 0.5 mm for power routing.
- Via Diameter: 0.7mm and Hole: 0.3 mm
## PCB Design
- Hide the fabrication layers (both front and back)
- If you have a monitor, split the screen in half such that left side, it is the schematic and the right side it is the PCB design.
- Orientation of the components make the routing task so easy, so do it smartly.
- By adding more traces, the capacitance value to a capacitor will decrease.
- Place the components wisely, the decoupling capacitors of the analog section have been placed such that very little routing is required later.
- Always start with sensitive circuitry like the decoupling capacitors, crystal oscillatary circuit.
- Using the rats nets (the thin lines which tell you to make the connections according to the schematic), you can place the component pretty accurately.
- You will atleast know where to place the components on the PCB.
- Ctrl+Shift+m will give the measuring ruler.
- For a pull up resistor for example, try to place it in line with the other two connections so that we have very little routing.
- Keep seeing the 3D view of your PCB. It helps because, it gives an idea about enclosure size.
- In our case, we have a few problems, the switch footprint is huge compared to other components.
- Also the USB connector is not showing up.
- You can change the footprint pretty easily, double click on the switch in the schematic, select the footprint option and then choose the right one.
- For the USB connector, 3D model, we have to add, it is readily available. Copy the manufacturers number, paste in the official website, download the stp file.
- Place it in the folder you are working in. Double click on the USB connector on the PDB design layout, go to 3D models, delete the existing ones, select the one which you just downloaded.
- The offsets and rotation might not be right, so you have to try out yourself by trial and error.
- When you go back to 3D view, now the 3d model of the USB connector will be visible. That's how you fix, 3D model issue.
- For this PCB, the board size after rearranging will be 25x35mm
- Move all the mounting holes to Fabrication layers.
- Drawing the outline for the PCB. Using the line and arc tools for rounded edges.
- Some text to be printed on the PCB board was hanging out, so you have to edit the footprint and delete the stuff.
- Place the connectors pretty close to the edge of the board.
## Routing
- Finish the critical circuitry first: oscillator circuitry and USB.
- For 2 layer board, start with ground plane on the bottom: whole copper plane tied to the ground net to provide a stable low impedance reference for the signal on top
- Adding a filled zone: On the bottom layer, select the filled zone type, Gnd, 0.3 mm clearance, 0.25 min width, pad connections: thermal reliefs, thermal relief gap: 0.5 mm and thermal spoke width: 0.5 mm. This helps in soldering the connectors.
- Around the outline of the board, draw the filled zone shape. Press 'b' to build.
- You can autoroute differential pair, this makes the routing look neat.
- Place vias near all ground connections. Don't place them on any silkscreen.
- We added a copper plane connection for some grounds.
- You can jump wires as you can but minimise the use as much as possible. While routing place a via an then route underneath.
- There we some minor errors, fixed them.
## Manufacturing
- Generate the Bill of materials, position file, gerber file and drill file.
- Check your gerber files using the gerber viewer option.
