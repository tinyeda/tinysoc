# TinySoC: Open RISC-V CPU

TinySoC is an open-source design for a RISC-V system on chip (SoC) CPU with custom built analog blocks.

## Background

Most *open* CPU designs usually include only the digital blocks required for a CPU, and rely on factory provided / proprietary IP for analog blocks.

There are a few reasons why most open source work has only been done on digital IP:-
1. Analog IP is highly process specific, so designs aren't portable across different manufacturing processes.
2. Some of it is usually provided by the fab since it is more complex and needs more testing.
3. People prefer buying hardened (extensively tested) analog IP blocks over developing their own since it is complex and time consuming.
4. Since the implementation of analog designs is highly linked to the manufacturing process you can't have an open analog block for a proprietary manufacturing process whereas you partially *could* for digital.

Therefore, there haven't been many great designs for open analog blocks for SoCs and most open exploration ends up being done for microarchitecture / digital design.

We now have everything needed to create a *completely* open chip:-
1. [Open PDKs](https://github.com/fossi-foundation/open-pdks)
2. [Open Instruction Set Architecture (RISC-V)](https://github.com/riscv/riscv-isa-manual)
3. [Open EDA Software](https://github.com/iic-jku/iic-osic-tools)

TinySoC is an attempt to create a CPU SoC with an emphasis on **analog infra*** required for an SoC.
This will likely be taped out using [Global Foundries' 180nm PDK](https://github.com/google/gf180mcu-pdk) since [cheap / convenient Multi Project Wafer (MPW) options](https://wafer.space/) are available.
Since this PDK doesn't have a lot of analog designs built on it we have a chance to build something new.

## Goals & Timeline

1. Design, test and manufacture required analog IP on individual tiles on TinyTapeout. (Q2/Q3 2027)
2. Develop / reuse a pre-made digital design for a [minimal working subset](https://docs.riscv.org/reference/home/) of the RISC-V ISA and integrate this with created analog IP.
3. Tapeout and verify the SoC including all digital and analog components! (Q4 2027)

## Draft of minimal required analog & digital components + specs

### Analog

1. **Power Delivery and Regulation**

   1. **Low-Dropout Regulator (LDO)** Generate clean, stable on-chip supply voltage via higher external supply.
   2. **Bandgap Reference** Provide stable reference voltage independent of manufacturing, input voltage and temperature variation [Experimental design taped out on gf180mcuD](https://github.com/bhagwat-rahul/ttgf180-bandgap-reference)
   3. **Power-Management Control and Sequencing** Ensure different power domains turn on/off in the correct order.
   4. **On-Chip Supply Decoupling** Reduce supply noise and provide short bursts of local current to switching circuits.
   5. **DC–DC Converter** Convert external supply into one or more internal supply voltages.

2. **Reset and Supply Monitoring**

   1. **Power-On Reset (PoR) Circuit** Hold SoC in reset until the power supply stable enough for reliable operation.
   2. **Brown-Out Detector and Reset** Reset SoC if supply voltage falls below safe operating level.
   3. **Supply-Voltage Monitor** Measure & flag if internal/external supply rails within acceptable limits.
   4. **Reset Synchronization and Distribution** Safely distribute reset signals across clock domains + throughout chip.

3. **Clock Generation and Distribution**

   1. **On-Chip Oscillator** Generate internal clock source signal.
   2. **PLLs / DLLs** Multiply, divide, align, or phase-shift clock(s) for higher-speed operation and interface timing.
   3. **Clock Gating / Clock-Domain Support** Reduce dynamic power and safely manage logic operating at different clock frequencies.
   4. **External Clock Input Cell** Accept and condition a clock signal supplied from outside the chip.

4. **Pad Ring and External I/O**

   1. **ESD Protection Circuits** Protect internal circuitry from electrostatic-discharge events at the package pins.
   2. **Latch-Up Prevention Structures** Prevent parasitic semiconductor structures from creating destructive high-current paths.
   3. **Pad Ring, Power Pads, and Ground Pads** Physical interface between internal chip circuitry & package.
   4. **Digital and Analog I/O Cells / Pads** Buffer, protect, and connect internal digital / analog signals to external pins.

5. **On-Chip Monitoring and Test**

   1. **Process, Voltage, and Temperature Monitors** Track manufacturing variation & operating conditions affecting circuit speed / reliability.
   2. **Ring Oscillators and Process-Characterization Structures** Measure process speed, device behavior, and variation across fabricated chips.
   3. **Analog Test Multiplexers** Route selected internal analog signals to test pads or measurement circuitry for characterization.

6. **Memory Infrastructure**
   *Possibly use PDK provided IP*

   1. **Sense Amplifiers** Detect & amplify small voltage differences produced when reading SRAM cells.
   2. **Memory Power Integrity** Ensure memory arrays receive stable power during simultaneous read / write activity.
   3. **Write Drivers** Force required voltages onto SRAM bitlines when storing data.

7. **Communication Interfaces**

   1. **UART / SPI** Serial Debug Interfaces
   2. **GPIOs** Configurable general purpose digital pins
   3. **High-Speed LVDS / SerDes PHY** High-speed communication (optional since this may take more time)

8. **Data Conversion**

   1. **Analog-to-Digital / Digital-to-Analog Converters**
   2. **Comparator** Determine if one analog voltage greater than another or crosses a defined threshold.
   3. **Analog Multiplexer** Select one of several analog signals for measurement or processing.
   4. **Sensor-Interface Front End** Preprocess sensor signals before using.


### Digital
1. Pipelined processor with arithmetic + floating point unit.
2. Support for RISC-V [Vector](https://docs.riscv.org/reference/isa/extensions/vector/_attachments/riscv-v-spec.pdf) and [Atomic](https://docs.riscv.org/reference/isa/v20260120/unpriv/a-st-ext.html) extensions.
3. Virtual memory support via a memory controller and SRAM based cache(s) + a small BootROM and possibly Direct Memory Access to bypass memory controller.
4. Interrupt / privilege system + hazard control.
5. Probably in order execution (can attempt out of order if time allows)

## Funding and Miscellaneous

Will require funds for tapeout and some hardware to test fabbed chips as well as funds to tapeout the final SoC on a half / full wafer.space slot.

Approximate Estimates
1. $3000 [Individual block tapeout cost with TinyTapeout tiles](https://app.tinytapeout.com/calculator?tiles=20&pcbs=4&shuttle=gf180)
2. $500 - $1000 (Lab equipment to test / characterize: Scope, Logic Analyzer, etc.)
3. $7000 + $1500 [Full slot on wafer.space + Chip On Board Packaging](https://wafer.space/price.html)

These costs will partially be funded by myself and possibly some grants. Some costs could also be offset by pre-selling final dev boards. All contributors will of course receive dev boards of the final product for no cost.

There is also an ongoing 2 year challenge called [Chipalooza](https://www.linkedin.com/posts/tim-edwards-4376a18_chipalooza-share-7483220065849614337-Nwgi/) with similar goals very kindly sponsoring good designs which can partially offset costs.
