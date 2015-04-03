# BMS Interface Board
A PCB to interface some of the main I/O functions of the Orion BMS with an electric car.

This project is developed by the Electric Car Club at the University of British Columbia, whose current goal is to break the world record for the fastest 1/4 mile in a street-legal electric car. The car, named Elektra, is a 1963 Volkswagen Beetle.

----------
FUNCTIONALITY
----------

The BMS Interface Board acts as a central physical hub to help integrate Ewert Energy's popular Orion BMS battery management system with existing circuits inside the car. It incorporates the following functionality:

- Provides fused power distribution to the BMS (+12V), an Orion Thermistor Expansion Module (+12V), and a Premo hall-effect current transducer (+/-15V) both whilst driving and charging.
- Interfaces the open drain Charge Safety, Discharge Enable, and Charge Enable outputs from the BMS with their corresponding circuits in the car via relays.
- Converts the +/-800mA output of the Premo current transducer to a 1-4V input signal for the BMS.
- Connects the Orion Thermistor Expansion Module to the BMS via the CAN1 bus and outputs CAN1 to a DB9 connector to interface with the Orion CANdapter (a CAN-to-USB adapter used for BMS programming purposes and firmware updates).

----------
CONTEXT
----------
The Orion BMS manages a battery pack consisting of 30 Turnigy 22.2V 6S 5Ah lithium-ion polymer batteries (originally designed for use in R/C airplanes) arranged in 5 series modules of 6 parallel batteries each. This produces a total nominal pack voltage of 111V with a peak discharge rate of around 2,400A.

The BMS monitors a variety of parameters, including total pack discharge, pack voltage, cell resistances, and state of charge. It is also responsible for monitoring the charging process of the pack, and is capable of balancing the individual cells within each battery. The addition of a Thermistor Expansion Module allows the BMS to monitor temperature data for each battery, which is a useful safety feature to detect abnormal battery behavior and take action to mitigate possible catastrophic failure before it occurs.

In order to properly monitor and protect the battery pack, the BMS collects data from a current transducer to monitor the current entering and leaving the pack. Ewert Energy Systems, the manufacturer of the Orion BMS, offers a range of sensors capable of measuring up to 1,000A of discharge current. As the UBC ECC's application calls for much higher discharge rates, however, we were forced to find an alternative solution. We decided to use a Premo HCT-2000LF, which is capable of measuring peak currents of +/-5,000A with 0.2% accuracy.
