Copyright Felix van Oost 2015.
This documentation describes Open Hardware and is licensed under the CERN OHL v1.2. You may redistribute and modify this documentation under the terms of the [CERN OHL v1.2](http://ohwr.org/cernohl). This documentation is distributed WITHOUT ANY EXPRESS OR IMPLIED WARRANTY, INCLUDING OF MERCHANTABILITY, SATISFACTORY QUALITY AND FITNESS FOR A PARTICULAR PURPOSE. Please see the CERN OHL v1.2 for applicable conditions.

# BMS Interface Board
A PCB to interface some of the main I/O functions of the [Orion BMS](http://www.orionbms.com/) with an electric car.

This project is developed by the [Electric Car Club](http://ubcelectriccar.com/) at the University of British Columbia, whose current goal is to break the world record for the fastest 1/4 mile in a street-legal electric car. The car, named Elektra, is based on a 1963 Volkswagen Beetle.

![Image of BMS Interface Board](https://raw.githubusercontent.com/FelixVanOost/BMS-Interface-Board/master/Photos%20%26%20Renderings/BMS%20Interface%20Board%20-%20Thumbnail.JPG)
*BMS Interface Board - Rev. A*

----------
Context
----------

In Elekta, the Orion BMS manages a battery pack consisting of 30 [Turnigy 22.2V 6S 5Ah](http://www.hobbyking.com/hobbyking/store/__38515__Turnigy_Heavy_Duty_Series_5000mAh_6S_60C_Lipo_Pack.html) lithium-ion polymer batteries (originally designed for use in R/C airplanes) arranged in 5 series modules of 6 parallel batteries each. This results in a total nominal pack voltage of 111V with a peak discharge current of around 2,400A.

The BMS monitors a variety of parameters, including total pack discharge, pack voltage, cell impedance, and state of charge. It is also responsible for monitoring the charging process of the pack, and is capable of balancing the individual cells within each battery. The addition of a [Thermistor Expansion Module](http://www.orionbms.com/products/thermistor-expansion-module/) allows the BMS to monitor temperature data for each battery, which is a useful safety feature to detect abnormal battery behavior and take action to mitigate possible catastrophic failure before it occurs. The Orion BMS and Thermistor Expansion Module are both powered from a nominal +12V. The BMS actually has two separate power inputs, which are used to place the unit in either charge or discharge mode.

In order to determine state of charge and peak charge / discharge current, the BMS uses a hall-effect current transducer mounted to the positive terminal of the entire battery pack. The transducer we use is a Premo HCT-2000LF, which is capable of measuring peak currents of +/-4,000A with 0.2% accuracy. The transducer requires a +/-15V bipolar power supply capable of delivering 1A continuously.

As a crucial safety measure, the BMS must be able to shut off power to both the battery pack and the charger to protect the batteries from damage. The Charge and Discharge Enable outputs on the BMS toggle on when the BMS determines that the battery pack is either ready to be charged or discharged. In Elektra, these outputs are connected to the main battery pack contactor circuit, which isolates the pack from the rest of the car. As such, the BMS has the ability to completely shut off power going into and out of the battery pack if it detects abornamal behaviour (e.g. the maximum discharge current limit has been exceeded or one of the batteries is getting too hot). The Charge Safety output is used as a backup safety measure to shut off charging current from entering the battery pack. Under normal circumstances, the Orion BMS communicates with the charger over CAN, controlling and monitoring the charging process. In this setup, the charger only has to provide the power that the BMS requests (the BMS has full control over the charging process). In a scenario where the BMS requests the charger to terminate charging and a malfunction or communication error occurs, however, the unit can toggle off the Charge Safety output to completely isolate the battery pack from the charger. In Elektra, this output is connected to a seperate charging contactor, which isolates the pack from the charging port on the side of the car.

----------
Functionality
----------

The BMS Interface Board acts as a central physical hub to help integrate the Orion battery management system with existing circuits inside the car. It incorporates the following functionality:

- Provides fused power distribution to the BMS (+12V), the Thermistor Expansion Module (+12V), and the hall-effect current transducer (+/-15V) both whilst driving and charging.
- Interfaces the open drain Charge Safety, Discharge Enable, and Charge Enable outputs from the BMS with their corresponding circuits in the car via relays.
- Converts the current output of the Premo current transducer to a 1-4V input signal for the BMS.
- Connects the Thermistor Expansion Module to the BMS via the CAN1 bus and outputs CAN1 to a DB9 connector to interface with the Orion CANdapter (a CAN-to-USB adapter used for BMS programming purposes and firmware updates).

----------
Specifications
----------

The BMS Interface Board incorporates a blend of both surface-mount and through-hole components based on their size and strength requirements. Surface-mount parts are 2012-sized where possible to simplify the PCB assembly process.

- An automotive-style 3A MINI blade fuse on the main +12V power rail protects the 12V circuitry in the car.

- To toggle the BMS between charge and discharge modes, an SPDT relay switches power to the appropriate power pin via a photocoupler and a set of current-limiting resistors. A flyback diode is incorporated into the design to prevent damage to the photocoupler as the relay is switched off. The BMS enters charge mode when the relay is switched off and discharge mode when the relay is switched on. The Interface Board accommodates 3.3V, 5V, or 12V inputs to switch the relay, which enables the BMS state to be set either by a microcontroller or a manual toggle switch connected to Elektras' 12V circuit.

- A pair of relays are used to interface the Charge Safety output to the charging contactor and the Discharge / Charge Enable outputs to the main contactors. The relays have been sized to accommodate a maximum switching current of 10A.

- A ±15V, 1A switching DC-DC converter from CUI provides power to the current transducer. A 10uF ceramic capacitor filters the input power to the unit, and a combination of 1uF ceramic and 100uF electrolytic capacitors provide filtering for the two outputs. The converter’s high efficiency (~90%) reduces the need to make special accommodations for heat dissipation on the board (total heat dissipated will peak at around 3W).

- The current transducer has a maximum current measurement capability of ±4,000A, and provides an output signal of ±800mA proportional to its input. The Orion BMS requires a 1-4V input signal (centred at 2.5V), where the range from 1-2.5V is ‘negative’ (charge) current and the remaining 2.5-4V is ‘positive’ (discharge) current. The BMS tolerates readings from 0.5-4.5V to extend the measurement range, but readings outside these bounds will trigger an internal fault. A 2.5Ω burden power resistor produces a ±2V output signal from the ±800mA current transducer output, following which a non-inverting amplifier with a gain of 0.75 converts the input to a ±1.5V signal and offsets the result by 2.5V to produce a 1-4V signal for the Orion BMS. The op-amp has been chosen for its low input offset voltage and fast slew rate to ensure accurate and fast current sensor readings with minimal error.

-	A set of six green LEDs are connected to each of the main supply rails (+12V, +15V, -15V, and +5V) as well as the Charge and Discharge Power outputs to provide visual user feedback for circuit troubleshooting purposes.

-	Molex Micro-Fit connectors are used for signal-level and low-power wiring, which offer a sturdy locking design in a compact package. The main power connector and current sensor connector are Molex Mega-Fit due to the higher currents and larger wire gauge requirement for those connections. All connectors are through-hole mount to ensure physical robustness.
