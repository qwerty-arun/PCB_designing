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
