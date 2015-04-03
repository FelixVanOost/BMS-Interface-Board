# BMS Interface Board
A PCB to interface some of the main I/O functions of the Orion BMS with an electric car.

This project is developed by the Electric Car Club at the University of British Columbia, whose current goal is to break the world record for the fastest 1/4 mile in a street-legal electric car. The car, named Elektra, is a 1963 Volkswagen Beetle.

----------
FUNCTIONALITY
----------

The BMS Interface Board acts as a central physical hub to help integrate Ewert Energy Systems's Orion battery management system with existing circuits inside the car. It incorporates the following functionality:

- Provides fused power distribution to the BMS (+12V), an Orion Thermistor Expansion Module (+12V), and a Premo hall-effect current transducer (+/-15V) both whilst driving and charging.
- Interfaces the open drain Charge Safety, Discharge Enable, and Charge Enable outputs from the BMS with their corresponding circuits in the car via relays.
- Converts the +/-800mA output of the Premo current transducer to a 1-4V input signal for the BMS.
- Connects the Orion Thermistor Expansion Module to the BMS via the CAN1 bus and outputs CAN1 to a DB9 connector to interface with the Orion CANdapter (a CAN-to-USB adapter used for BMS programming purposes and firmware updates).

----------
CONTEXT
----------

The Orion BMS manages a battery pack consisting of 30 Turnigy 22.2V 6S 5Ah lithium-ion polymer batteries (originally designed for use in R/C airplanes) arranged in 5 series modules of 6 parallel batteries each. This produces a total nominal pack voltage of 111V with a peak discharge rate of around 2,400A.

The BMS monitors a variety of parameters, including total pack discharge, pack voltage, cell resistances, and state of charge. It is also responsible for monitoring the charging process of the pack, and is capable of balancing the individual cells within each battery. The addition of a Thermistor Expansion Module allows the BMS to monitor temperature data for each battery, which is a useful safety feature to detect abnormal battery behavior and take action to mitigate possible catastrophic failure before it occurs. The Orion BMS and Thermistor Expansion Module are both powered from a nominal +12V. The BMS actually has two separate power inputs, which are switched to place the unit in either charge or discharge mode.

In order to determine state of charge and peak charge / discharge current, the BMS uses a hall-effect current transducer mounted to either the positive or negative terminal of the whole battery pack. Ewert Energy Systems offers a range of transducers capable of measuring up to 1,000A of discharge current. As the UBC ECC's application calls for much higher discharge rates, however, we were forced to find an alternative solution. The transducer we finally decided on is a Premo HCT-2000LF, which is capable of measuring peak currents of +/-5,000A with 0.2% accuracy. The transducer requires a +/-15V bipolar power supply capable of delivering 1A continuously.

As a crucial safety measure, the BMS must be able to shut off power to both the battery pack and the charger to protect the batteries from damage. The Charge and Discharge Enable outputs on the BMS toggle on when the BMS determines that the battery pack is either ready to be charged or discharged. In Elektra, these outputs are connected to the main battery pack contactor circuit, which isolates the pack from the rest of the car. As such, the BMS has the capability of completely shutting off power going into and out of the battery pack if it detects abornamal behaviour (e.g. the maximum discharge current limit has been exceeded or one of the batteries is getting too hot). The Charge Safety output is used as a backup safety measure to shut off charging current from entering the battery pack. Under normal circumstances, the Orion BMS communicates with a charger over CAN, controlling and monitoring the charging process. In this setup, the charger only has to provide the power that the BMS requests. In a scenario where the BMS requests the charger to terminate charging and a malfunction or communication error occurs, however, the unit can toggle off the Charge Safety output to completely isolate the battery pack from the charger. In Elektra, this output is connected to a seperate charging contactor, which isolates the pack from the charging port on the side of the car.
