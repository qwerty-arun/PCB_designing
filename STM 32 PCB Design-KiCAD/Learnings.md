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
