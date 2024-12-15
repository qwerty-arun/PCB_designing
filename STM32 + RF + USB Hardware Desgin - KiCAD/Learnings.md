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
