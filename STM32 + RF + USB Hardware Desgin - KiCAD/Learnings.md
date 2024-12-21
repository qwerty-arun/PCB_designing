# [Reference](https://www.youtube.com/watch?v=14_jh3nLSsU&list=PLXSyc11qLa1b9VA7nw8-DiLRXVhZ2iUN2&index=5)
# [Github](https://github.com/pms67/STRF-Kicad/tree/master)
## Overview
- Main components: STM33 mc, RF transceiver, antenna interface, USB full speed interface
- RF - Radio frequency involves high frequencies, so the design requires careful designing
- This board has many applications: say a robot has a data is continuously taking more values, it has RF transceiver to send the data, this module will pick up the data, process it with STM32, send it to a host computer via the USB
- [nRF24L01](https://www.sparkfun.com/datasheets/Components/SMD/nRF24L01Pluss_Preliminary_Product_Specification_v1_0.pdf) : RF transceiver working at 2.4GHz
- [STM32L432KB](https://www.st.com/resource/en/datasheet/stm32l432kc.pdf): Low power MC, running at 80MHz
- Instead of a PCB, the author went for a SMA connector, in which it is possible to connect various types of passive 2.4GHz antennas. SMA: SubMiniature version A
- Two LEDs on board, one for RX and the other for TX, which is a good indicator
- SPI is chose with a mode of full duplex master, some extra GPIO pins, USB pins, that's how the pinout will look like.
# Power Section
- USB has a 5V connection to it, but we need 3V to drive the processor and the transciever
- USB requires exactly 3.3V for operation according to the datasheet
- Max value of decoupling capacitors should not be more than 10u, otherwise when the board is turned, the in-rush current will be too high which won't be good.
- Generally good to put a reverse polarity protection, but here we are using USB connector, so chances are very low.
- [XC2606P322MR Datasheet](https://wmsc.lcsc.com/wmsc/upload/file/pdf/v2/lcsc/2109092030_Torex-Semicon-XC6206P282MR-G_C2891263.pdf)

# MCU 
- [STM32 datasheet](https://www.st.com/content/ccc/resource/technical/document/datasheet/24/01/9f/59/f0/83/47/fc/DM00257205.pdf/files/DM00257205.pdf/jcr:content/translations/en.DM00257205.pdf)
- 3 decoupling capacitor each for a vdd pin and a bulk capacitor nearby
- Boot pin determines if boot loader is started or not, and is pulled low in our case.
- We will be programming with SWD and not via the USB/UART. Set high if you are using them.
- We used 2 LEDs with current limiting resistors.
- Calculation: 3.3 V max voltage at GPIO when high, R = (3.3 - 1.8)/1mA, where 1.8V is the drop across LED, 1mA chosen according to datasheet.
# SWD (Serial-Wire-Debug Connector)
- Arm pin 10 swd
- Copy that pin-out over to that schematic and that will fit with the st-link adapter.
- Go to Github and see the raw file, you can literally see what connections are made in the schematic
- That's how I found out what conector to use for the SWD
- It was 2x5 connector pin: Conn_02x05_Odd_Even
- The capacitor attached to NRST is a debouncing capacitor
- If someone touches the pin with their hand, it might reset the device, it protects against that
# USB Connection and ESD Protection
- We humans carry a lot of static charges, so this little chip protects this while we are plugging USB
- Shield pin of USB is not connected here for following reasons: Shield is grounded at the host side (PC)
# Transciever Section
- [nRF24L01p](https://www.sparkfun.com/datasheets/Components/nRF24L01_prelim_prod_spec_1_2.pdf)
- Check out the application example, section it in the schematic to make it look a little bit nicer.
- Done with the sectioning and labeling
# Transceiver Crystal
- Calculation of load and stray capacitance (2-5pF): C = 2 * (Cload - Cstray)
- Based on the application circuit
# Antenna Impedance matching and SMA connector
- We have 3 inductors, 4 capacitors, one SMA connector
- The chip will have a certain output impedance say, 1kHz, we need to match this with 50 Ohms since antennas generally have 50 Ohms resistance.
- We have a differential ouptut from the board, but we have a single antenna (so even the network converts the antenna into a differential pair)
# Assigning footprints to the components
- Since, we are making a small board, we will be using 0402 SMD size specs
- Especially for the RF section, this is also mentioned in the datasheet
- Anything that needs to handle more current or display more power, we will use 0603. Example: for LEDs, the bulk capacitor for the power section
- 1206 is used for the fuse just in case it burns out, it can be easily replaced.
