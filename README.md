\# AeroCore Flight Controller



An ESP32-based quadcopter flight-controller system integrating inertial sensing, altitude and heading measurement, GPS navigation, motor control, battery monitoring, power management, wireless communication, and peripheral interfaces on a custom PCB.



\## Overview



AeroCore Flight Controller is a custom ESP32-based flight-control platform designed for a four-motor quadcopter. The system combines the processing, sensing, power-management, communication, and control interfaces required to operate a multirotor aircraft.



The flight controller is designed around the \*\*ESP32-WROOM-32\*\* and provides the central processing platform for sensor acquisition, attitude estimation, flight-control algorithms, motor mixing, telemetry, battery monitoring, and system management.



The hardware architecture is organized around dedicated sensing, power, communication, and propulsion interfaces while keeping the high-current motor power stages external to the flight-controller PCB.



\## System Architecture



```text

&#x20;                        ┌──────────────────────┐

&#x20;                        │      LiPo Battery    │

&#x20;                        └──────────┬───────────┘

&#x20;                                   │

&#x20;                        ┌──────────▼───────────┐

&#x20;                        │ Power Distribution   │

&#x20;                        │ \& Protection         │

&#x20;                        └─────┬────────┬───────┘

&#x20;                              │        │

&#x20;                        Motor Power   FC Power

&#x20;                              │        │

&#x20;                   ┌──────────▼──┐  ┌──▼──────────────┐

&#x20;                   │ 4 × ESCs    │  │ Power Regulators│

&#x20;                   └─────┬───────┘  └──────┬─────────┘

&#x20;                         │                  │

&#x20;                   ┌─────▼─────┐     ┌────▼──────────┐

&#x20;                   │ 4 × BLDC  │     │ 3.3 V / 5 V   │

&#x20;                   │  Motors   │     │ Supply Rails  │

&#x20;                   └───────────┘     └────┬──────────┘

&#x20;                                          │

&#x20;                                   ┌──────▼───────┐

&#x20;                                   │ ESP32-WROOM  │

&#x20;                                   │ Flight MCU   │

&#x20;                                   └──┬──┬──┬──┬──┘

&#x20;                                      │  │  │  │

&#x20;                        ┌─────────────┘  │  │  └─────────────┐

&#x20;                        │                │  │                │

&#x20;                   ┌────▼────┐      ┌────▼──▼────┐      ┌────▼────┐

&#x20;                   │ MPU6050 │      │ BMP280     │      │ QMC5883L│

&#x20;                   │ IMU     │      │ Barometer  │      │ Compass  │

&#x20;                   └─────────┘      └────────────┘      └─────────┘

&#x20;                        │

&#x20;                   ┌────▼────┐

&#x20;                   │ GPS/GNSS│

&#x20;                   │ NEO-M8N │

&#x20;                   └─────────┘

```



\## Main Controller



\### ESP32-WROOM-32



The ESP32-WROOM-32 acts as the central flight-control processor.



Primary functions include:



\- Sensor acquisition

\- Attitude estimation

\- Flight-control calculations

\- PID control loops

\- Motor mixing

\- ESC command generation

\- RC receiver processing

\- Battery monitoring

\- GPS processing

\- Telemetry

\- System diagnostics

\- Failsafe management

\- Wireless configuration and monitoring



The ESP32 provides integrated Wi-Fi and Bluetooth/BLE connectivity together with multiple UART, SPI, I²C, ADC, PWM, and GPIO peripherals.



\## Inertial Measurement Unit



\### MPU6050



The MPU6050 provides the primary inertial measurements required for flight stabilization.



It combines:



\- 3-axis accelerometer

\- 3-axis gyroscope



The sensor communicates with the ESP32 through I²C.



The IMU provides the flight controller with:



\- Linear acceleration

\- Angular velocity

\- Roll information

\- Pitch information

\- Yaw-rate information



Sensor data is filtered and processed by the flight-control firmware to estimate aircraft attitude and motion.



\## Barometric Altitude Sensor



\### BMP280



The BMP280 provides atmospheric pressure measurements for relative altitude estimation.



It communicates with the ESP32 through I²C and supports:



\- Altitude estimation

\- Vertical-motion monitoring

\- Altitude-hold control

\- Pressure-based flight information



The barometer is positioned away from direct airflow and vibration sources to improve measurement stability.



\## Magnetic Heading Sensor



\### QMC5883L



The QMC5883L three-axis magnetometer provides magnetic-field measurements for heading estimation.



It can support:



\- Compass heading

\- Yaw reference

\- Navigation

\- Heading-assisted flight modes



The magnetometer is electrically connected through I²C and should be physically positioned away from high-current motor and power paths to reduce magnetic interference.



\## GPS / GNSS



\### u-blox NEO-M8N



The NEO-M8N GPS/GNSS module provides outdoor positioning information.



The GPS interface communicates with the ESP32 through UART.



The positioning subsystem supports:



\- Latitude and longitude

\- Ground speed

\- Altitude

\- Direction of travel

\- Position monitoring

\- Return-to-home functionality

\- Position-assisted flight

\- Navigation and future autonomous-flight functions



\## Motor-Control System



The flight controller provides four independent motor-control channels.



```text

&#x20;                ESP32

&#x20;                  │

&#x20;      ┌───────────┼───────────┐

&#x20;      │           │           │

&#x20;     M1          M2          M3          M4

&#x20;      │           │           │           │

&#x20;     ESC         ESC         ESC         ESC

&#x20;      │           │           │           │

&#x20;  Motor 1      Motor 2      Motor 3      Motor 4

```



Each output controls one ESC.



The four channels correspond to:



\- Motor 1 — Front Left

\- Motor 2 — Front Right

\- Motor 3 — Rear Right

\- Motor 4 — Rear Left



The firmware performs motor mixing to convert throttle, roll, pitch, and yaw commands into individual motor outputs.



\## Electronic Speed Controllers



Four external ESCs control the brushless propulsion motors.



Each ESC receives a control signal from the flight controller and directly handles the high-current motor drive.



The ESC system provides:



\- Motor commutation

\- Speed control

\- High-current switching

\- Battery-to-motor power conversion



The flight-controller PCB only carries the low-power control and monitoring interfaces while the high-current propulsion paths remain external.



\## Brushless Motors



The propulsion system uses four brushless DC motors selected according to:



\- Aircraft mass

\- Propeller size

\- Battery voltage

\- Required thrust

\- Target flight time



Two motors rotate clockwise and two rotate counter-clockwise.



The opposing rotation arrangement produces balanced torque and enables yaw control through differential motor speed.



\## Propellers



The quadcopter uses four propellers:



\- Two clockwise propellers

\- Two counter-clockwise propellers



Propeller selection is matched to motor KV rating, battery voltage, frame dimensions, and required thrust.



Correct motor direction and propeller orientation are essential for stable flight.



\## Battery System



\### LiPo Battery



The propulsion system is powered by a rechargeable LiPo battery pack.



The battery supplies:



\- Four ESCs

\- Four brushless motors

\- Flight-controller power system



A battery configuration such as a \*\*3S or 4S LiPo pack\*\* can be selected according to the motor and ESC specifications.



The flight controller receives regulated power through its power-management circuitry rather than directly applying the raw propulsion voltage to the ESP32.



\## Power Management



The PCB contains separate power-conversion stages for the digital electronics and peripheral devices.



\### MP1584 Buck Converter



An MP1584-based buck stage provides efficient step-down conversion from the main battery supply.



The regulated supply can feed the 5 V subsystem and downstream peripherals.



\### AMS1117-3.3 Regulator



An AMS1117-3.3 regulator provides a regulated 3.3 V rail for the ESP32 and compatible low-voltage sensors.



The design includes appropriate input and output capacitors for regulator stability and transient filtering.



\## Power Protection



The power-input section incorporates protection and filtering including:



\- Input filtering

\- Bulk capacitance

\- Ceramic decoupling

\- Reverse-polarity protection

\- Transient suppression

\- Fuse or current protection

\- Separate high-current and low-current power paths



Power and ground distribution are designed to minimize voltage drops and electrical noise reaching sensitive sensor circuitry.



\## Battery Voltage Monitoring



The ESP32 monitors the propulsion battery through a resistor-divider network connected to an ADC input.



The voltage divider scales the battery voltage into the safe measurement range of the ESP32 ADC.



Battery monitoring supports:



\- Real-time voltage measurement

\- Low-battery warnings

\- Battery telemetry

\- Flight logging

\- Failsafe decisions



\## Current Monitoring



A current-sensing circuit such as the \*\*INA219\*\* provides battery current and bus-voltage measurements.



The INA219 communicates with the ESP32 through I²C.



Measured parameters include:



\- Bus voltage

\- Load current

\- Power consumption



This information can be used to estimate energy consumption and improve battery management.



\## RC Receiver



The flight controller provides a UART-based interface for an external RC receiver.



A receiver supporting protocols such as \*\*CRSF, SBUS, or equivalent serial control protocols\*\* can provide:



\- Throttle

\- Roll

\- Pitch

\- Yaw

\- Flight-mode selection

\- Auxiliary commands



The firmware monitors receiver communication and activates failsafe behavior when the control link is lost.



\## Telemetry and Wireless Communication



The ESP32's integrated wireless interfaces provide development and telemetry capabilities.



\### Wi-Fi



Wi-Fi can be used for:



\- Configuration

\- Telemetry

\- Diagnostics

\- Firmware development

\- Ground-station communication



\### Bluetooth / BLE



Bluetooth/BLE can provide short-range configuration and diagnostic communication.



\### UART



UART interfaces support:



\- GPS

\- RC receiver

\- External telemetry

\- Debugging

\- Additional serial peripherals



\### I²C



I²C is used for low-speed peripheral devices including:



\- MPU6050

\- BMP280

\- QMC5883L

\- INA219



\### SPI



SPI is reserved for high-speed peripherals and future expansion.



\## USB Programming and Debugging



The controller provides a USB/UART programming interface for firmware development and debugging.



The programming interface includes:



\- USB connection

\- USB-to-UART bridge

\- ESP32 UART interface

\- Boot-mode control

\- Reset control



This allows firmware to be uploaded and serial diagnostic information to be monitored during development.



\## Status Indicators



The controller includes LEDs for basic system feedback.



Indicators can represent:



\- Power

\- ESP32 activity

\- GPS status

\- Communication status

\- Flight-controller state

\- Fault conditions



\## Buzzer



A buzzer provides audible system feedback.



Possible indications include:



\- Startup

\- Initialization

\- Armed/disarmed state

\- Low battery

\- Communication failure

\- System fault

\- GPS status



\## Expansion Interfaces



The PCB provides expansion capability for additional sensors and peripherals.



Potential extensions include:



\- Optical-flow sensors

\- Time-of-flight distance sensors

\- External magnetometers

\- Additional GPS modules

\- External telemetry radios

\- SD-card data logging

\- Camera systems

\- Companion computers

\- Additional environmental sensors



\## Flight-Control Software



The embedded firmware is responsible for the complete flight-control process.



The software architecture includes:



\### Sensor Acquisition



The ESP32 continuously reads:



\- Accelerometer

\- Gyroscope

\- Barometer

\- Magnetometer

\- GPS

\- Battery voltage

\- Battery current



\### Sensor Processing



Raw measurements are filtered to reduce:



\- Electrical noise

\- Mechanical vibration

\- Sensor noise

\- Measurement spikes



Attitude estimation combines accelerometer and gyroscope data to determine the aircraft's orientation.



\### Flight-Control Loops



PID controllers are used to regulate:



\- Roll

\- Pitch

\- Yaw

\- Altitude where supported



The controller compares the desired state with the measured state and calculates corrective motor commands.



\### Motor Mixing



The calculated throttle, roll, pitch, and yaw corrections are converted into four individual motor commands.



This allows the aircraft to maintain stability and respond to pilot inputs.



\### Failsafe



The system monitors critical operating conditions including:



\- RC communication

\- Battery voltage

\- Sensor availability

\- GPS status

\- Controller state



Fault conditions can trigger predefined failsafe behavior.



\## PCB Design



The flight controller is designed using KiCad.



The PCB design covers:



\- Complete electrical schematic

\- Component selection

\- Power architecture

\- ESP32 circuitry

\- Sensor interfaces

\- Motor-control outputs

\- RC receiver interface

\- GPS interface

\- Battery monitoring

\- Current sensing

\- Communication interfaces

\- Programming interface

\- PCB layout

\- Ground planes

\- Decoupling

\- Signal routing

\- Design-rule checking

\- Manufacturing preparation



Special consideration is given to separating noisy switching and high-current sections from sensitive inertial and analog measurement circuitry.



\## Hardware Design Considerations



The PCB incorporates engineering practices including:



\- Local decoupling at IC power pins

\- Bulk capacitance on supply rails

\- Short high-current paths

\- Dedicated ground-return paths

\- Proper regulator filtering

\- Analog-signal protection

\- EMI/noise reduction

\- Thermal considerations

\- Connector strain and accessibility

\- Programming access

\- Sensor placement

\- Mechanical mounting considerations



\## Project Structure



```text

AeroCore-Flight-Controller/

│

├── AeroCore-Flight-Controller.kicad\_pro

├── AeroCore-Flight-Controller.kicad\_sch

├── AeroCore-Flight-Controller.kicad\_pcb

├── ESP32.kicad\_sch

├── Peripherals.kicad\_sch

├── Power Management.kicad\_sch

├── README.md

└── .gitignore

```



\## Development Workflow



The project follows a hardware-development workflow:



1\. System requirements

2\. Component selection

3\. Electrical architecture

4\. Schematic design

5\. Power analysis

6\. PCB placement

7\. PCB routing

8\. Design-rule checking

9\. Manufacturing preparation

10\. PCB fabrication

11\. Component assembly

12\. Power-on testing

13\. Sensor validation

14\. ESC and motor testing

15\. Firmware development

16\. Controlled flight testing

17\. System refinement



\## Project Status



\*\*In Development\*\*



The project is being developed as a custom ESP32 flight-controller platform. The PCB design forms the hardware foundation for subsequent firmware development, fabrication, bench testing, and flight validation.



\## Objectives



The main objectives are to develop practical experience in:



\- Embedded systems

\- ESP32 hardware design

\- PCB design

\- Sensor integration

\- Power electronics

\- Motor-control systems

\- Battery management

\- Wireless communication

\- Flight-control architecture

\- Engineering documentation



\## Tools



\- KiCad

\- ESP32

\- C/C++

\- Git

\- GitHub



\## Author



\*\*Henry Oyoto\*\*



Electronics and Computer Engineering



GitHub: \[HenryOyoto](https://github.com/HenryOyoto)



Portfolio: \[henryoyoto.github.io](https://henryoyoto.github.io)

