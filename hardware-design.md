# Hardware Design and System Integration

## Cranial Mechanical Resonance Mapping (CMRM)

**Document Type:** Hardware Engineering Documentation
**Project:** Cranial Mechanical Resonance Mapping
**System Category:** Embedded Biomedical Instrumentation
**Architecture:** ESP32-Based Multi-Sensor Measurement Platform
**Development Stage:** Experimental Prototype

---

# 1. Introduction

The hardware subsystem of the Cranial Mechanical Resonance Mapping (CMRM) platform is designed to acquire mechanical vibration measurements from multiple locations of a cranial headform.

The system integrates an embedded controller, multiple digital accelerometers, an I²C communication multiplexer, a mechanical excitation subsystem and a rechargeable power-management circuit.

The ESP32 acts as the central controller, coordinating sensor communication, experimental control and data transmission to the desktop application.

Four ADXL345 accelerometers are proposed for spatially distributed vibration measurement. The TCA9548A multiplexer manages communication with the individual sensor modules.

A separate excitation subsystem generates mechanical vibration through a bone-conduction transducer.

The complete hardware architecture is divided into five primary subsystems:

1. Power Management.
2. Embedded Control Unit.
3. Mechanical Excitation.
4. Multi-Sensor Acquisition.
5. Communication and Data Interface.

---

# 2. Overall Hardware Architecture

The proposed hardware architecture consists of the following components:

| Subsystem          | Main Components                                          |
| ------------------ | -------------------------------------------------------- |
| Power Management   | 3.7V Battery, TP4056, 5V Regulator                       |
| Control Unit       | ESP32 Development Board                                  |
| Excitation Path    | ESP32 PWM, PAM8403 Amplifier, Bone-Conduction Transducer |
| Sensor Interface   | TCA9548A I²C Multiplexer                                 |
| Sensor Acquisition | Four ADXL345 Accelerometers                              |
| Communication      | USB, Serial Interface, Optional Wi-Fi/Bluetooth          |

The ESP32 coordinates the different hardware subsystems and provides the interface between physical measurements and the software environment.

---

# 3. Embedded Control Unit

## 3.1 ESP32 Microcontroller

The ESP32 is the central processing and communication unit of the CMRM platform.

It provides the control interface for the sensor acquisition system and excitation subsystem.

### Primary Responsibilities

* Multi-sensor communication.
* I²C bus management.
* PWM signal generation.
* Sensor acquisition coordination.
* Experimental parameter management.
* Serial communication.
* Data transmission.
* Wireless communication when enabled.

### Key Features

| Feature           | Description                                         |
| ----------------- | --------------------------------------------------- |
| Processor         | Dual-core architecture on applicable ESP32 variants |
| Wireless          | Wi-Fi and Bluetooth capabilities                    |
| Communication     | I²C, SPI, UART                                      |
| PWM               | LEDC peripheral on supported ESP32 variants         |
| Programming       | USB-to-serial interface                             |
| Operating Voltage | 3.3V logic                                          |

Exact capabilities depend on the ESP32 development-board variant.

## 3.2 ESP32 Functional Architecture

The ESP32 is responsible for coordinating:

1. Sensor communication.
2. I²C multiplexer selection.
3. Excitation signal generation.
4. Experimental timing.
5. Data acquisition.
6. Communication with desktop software.

The controller should also monitor communication errors and hardware availability.

---

# 4. Multi-Sensor Acquisition System

## 4.1 ADXL345 Accelerometer

The ADXL345 is a three-axis digital accelerometer used to measure acceleration along three orthogonal axes.

Each sensor provides measurements along:

* X-axis.
* Y-axis.
* Z-axis.

These measurements are used to characterize vibration responses at different locations of the cranial headform.

### Sensor Configuration

The proposed system contains four ADXL345 sensor modules.

| Sensor ID | Placement      | Measurement Objective          |
| --------- | -------------- | ------------------------------ |
| S1        | Left Temporal  | Left-side mechanical response  |
| S2        | Right Temporal | Right-side mechanical response |
| S3        | Frontal        | Frontal mechanical response    |
| S4        | Occipital      | Posterior mechanical response  |

### Measurement Parameters

The accelerometer measurements can be used to calculate:

* Axis-specific acceleration.
* Resultant acceleration.
* RMS acceleration.
* Peak acceleration.
* Peak-to-peak acceleration.
* Frequency-domain characteristics.

### Resultant Acceleration

The resultant acceleration is calculated as:

$$
A_r = \sqrt{A_x^2+A_y^2+A_z^2}
$$

Where:

* \(A_x\) = X-axis acceleration.
* \(A_y\) = Y-axis acceleration.
* \(A_z\) = Z-axis acceleration.

The resulting value represents the combined acceleration magnitude.

Sensor orientation and gravitational baseline must be considered when interpreting measurements.

## 4.2 Sensor Placement Considerations

Sensor placement is important because mechanical coupling directly influences the measured vibration response.

The following parameters should be documented:

* Sensor mounting location.
* Sensor orientation.
* Mounting material.
* Mechanical contact condition.
* Sensor identification.
* Calibration parameters.

All sensors should use consistent mounting procedures to improve experimental repeatability.

---

# 5. I²C Multiplexer

## 5.1 TCA9548A

The TCA9548A is an eight-channel I²C multiplexer used to manage communication with multiple sensor modules.

In CMRM, it provides separate communication channels for the ADXL345 accelerometers.

### Purpose

The multiplexer is used to:

* Select individual sensor channels.
* Manage sensors with identical I²C addresses.
* Reduce I²C address conflicts.
* Organize multi-sensor communication.

### Proposed Channel Allocation

| Multiplexer Channel | Connected Sensor    |
| ------------------- | ------------------- |
| Channel 0           | S1 – Left Temporal  |
| Channel 1           | S2 – Right Temporal |
| Channel 2           | S3 – Frontal        |
| Channel 3           | S4 – Occipital      |

The remaining channels can be reserved for future expansion.

### Communication Workflow

1. ESP32 initializes the I²C interface.
2. ESP32 communicates with the TCA9548A.
3. The required multiplexer channel is selected.
4. The corresponding ADXL345 is accessed.
5. Acceleration measurements are collected.
6. The next channel is selected.
7. The process repeats.

### Important Synchronization Consideration

The TCA9548A performs channel selection sequentially.

Therefore, readings from the four accelerometers are not automatically simultaneous.

For accurate multi-sensor analysis, the firmware should record timestamps for individual measurements and characterize acquisition timing differences.

Phase comparisons and time-delay calculations should not be treated as valid without appropriate synchronization and correction.

---

# 6. Mechanical Excitation System

## 6.1 Overview

The excitation subsystem is designed to generate controlled mechanical vibration through a bone-conduction transducer.

The proposed excitation path consists of:

ESP32 PWM → PAM8403 Amplifier → Bone-Conduction Transducer.

The objective is to introduce a controlled mechanical input into the cranial headform and measure the resulting vibration response using the distributed accelerometers.

## 6.2 ESP32 PWM Generation

The ESP32 generates a PWM-based excitation control signal.

The excitation parameters may include:

* Frequency.
* Duty cycle.
* Duration.
* Sweep interval.
* Signal pattern.

A controlled frequency sweep can be implemented by varying the excitation frequency according to a predefined experimental protocol.

**Note:** PWM frequency and waveform characteristics must be verified. A filtered or DAC-generated waveform may be necessary depending on the required excitation quality.

## 6.3 PAM8403 Amplifier

The PAM8403 is a Class-D audio amplifier module.

Its proposed role is to amplify the electrical excitation signal before it reaches the transducer.

Important considerations:

* Supply voltage compatibility.
* Input signal characteristics.
* Output power.
* Load impedance.
* Frequency response.
* Thermal behaviour.
* Electrical noise.

The suitability of this amplifier for the selected bone-conduction transducer must be experimentally verified.

It should not be assumed that the amplifier reproduces every required sweep frequency accurately.

## 6.4 Bone-Conduction Transducer

The bone-conduction transducer converts electrical energy into mechanical vibration.

It acts as the mechanical excitation source in the experimental setup.

Important characterization parameters include:

* Operating frequency range.
* Electrical impedance.
* Input power rating.
* Mechanical output.
* Mounting configuration.
* Mechanical coupling.
* Frequency-dependent response.

The transducer's nominal resonance or specified operating frequency must not automatically be interpreted as the resonance frequency of the cranial headform.

Independent measurement of the delivered excitation is necessary.

---

# 7. Power Management System

## 7.1 Overview

The power-management subsystem provides electrical power to the embedded controller, sensors and excitation electronics.

The proposed architecture contains:

* 3.7V rechargeable lithium-ion battery.
* TP4056 charging module.
* 5V voltage regulation stage.
* Power distribution circuitry.

## 7.2 Lithium-Ion Battery

The battery acts as the primary portable energy source.

Its selection should consider:

* Nominal voltage.
* Capacity.
* Maximum discharge current.
* Protection circuitry.
* Charging requirements.
* Operating duration.

## 7.3 TP4056 Charging Module

The TP4056 is a single-cell lithium-ion battery charging controller.

Its purpose is to manage battery charging.

It should not be treated as a 5V voltage regulator.

The battery output must be connected to a separately designed and appropriately rated voltage-regulation stage.

## 7.4 Voltage Regulation

The proposed system includes a 5V regulation stage.

The regulator must be selected according to:

* Input voltage range.
* Output voltage.
* Maximum current.
* Conversion efficiency.
* Thermal dissipation.
* Ripple characteristics.

The ESP32 development board and sensor modules require their appropriate regulated supply rails.

The PAM8403 amplifier must receive a supply within its specified operating range.

Separate power rails may be appropriate for the sensitive measurement electronics and excitation circuitry.

### Power Distribution Considerations

* Verify voltage compatibility for every module.
* Prevent reverse-polarity connections.
* Use appropriate overcurrent protection.
* Minimize power-supply noise.
* Provide adequate decoupling.
* Verify grounding and return-current paths.
* Avoid overloading the regulator.

The final power architecture must be validated against actual current consumption.

---

# 8. Communication Interfaces

## 8.1 I²C Communication

I²C is used for communication between the ESP32, TCA9548A and ADXL345 sensor modules.

The bus requires:

* SDA.
* SCL.
* Common ground.
* Appropriate pull-up resistors.
* Compatible logic voltage.

The I²C bus frequency should be selected according to the capabilities of all connected devices and the actual wiring configuration.

## 8.2 USB Communication

USB provides the primary development and debugging interface.

It can support:

* Firmware uploading.
* Serial monitoring.
* Hardware debugging.
* Data communication with desktop software.

## 8.3 Wireless Communication

The ESP32 supports wireless communication on applicable variants.

Wi-Fi and Bluetooth may be used for optional data transmission.

Wireless communication should be disabled when unnecessary and protected through appropriate authentication and encryption mechanisms when enabled.

---

# 9. Hardware Integration Workflow

The proposed integration sequence is:

### Stage 1: Controller Setup

* Configure ESP32.
* Verify USB communication.
* Test I²C initialization.
* Verify PWM generation.

### Stage 2: Sensor Integration

* Connect the TCA9548A.
* Connect individual ADXL345 modules.
* Verify sensor addresses.
* Test individual sensor readings.
* Validate channel selection.

### Stage 3: Power Integration

* Configure the battery charging circuit.
* Verify voltage regulation.
* Measure current consumption.
* Validate power distribution.

### Stage 4: Excitation Integration

* Configure the excitation control signal.
* Verify amplifier compatibility.
* Characterize transducer output.
* Test the mechanical coupling on a suitable headform.

### Stage 5: Complete System Integration

* Integrate the sensor network.
* Verify data acquisition.
* Characterize timing differences.
* Connect the desktop application.
* Perform system-level testing.

---

# 10. Hardware Validation

Hardware validation is required before conducting meaningful mechanical experiments.

## 10.1 ESP32 Validation

* [ ] Verify development-board variant.
* [ ] Verify USB communication.
* [ ] Verify I²C initialization.
* [ ] Verify PWM output.
* [ ] Verify supply voltage.

## 10.2 Sensor Validation

* [ ] Detect all four ADXL345 modules.
* [ ] Verify sensor identity.
* [ ] Check X, Y and Z readings.
* [ ] Verify sensor orientation.
* [ ] Perform calibration.
* [ ] Evaluate measurement noise.

## 10.3 Multiplexer Validation

* [ ] Detect TCA9548A.
* [ ] Verify channel selection.
* [ ] Test individual sensor channels.
* [ ] Check for address conflicts.
* [ ] Measure acquisition timing.

## 10.4 Excitation Validation

* [ ] Verify PWM signal characteristics.
* [ ] Verify amplifier supply.
* [ ] Confirm transducer compatibility.
* [ ] Measure delivered excitation.
* [ ] Evaluate frequency response.
* [ ] Check thermal behaviour.

## 10.5 Power Validation

* [ ] Verify battery voltage.
* [ ] Verify charging operation.
* [ ] Verify regulator output.
* [ ] Measure current consumption.
* [ ] Check power stability.
* [ ] Verify protection mechanisms.

---

# 11. Electrical Safety and Experimental Considerations

The CMRM hardware is intended for controlled engineering experimentation.

The initial testing environment should use suitable non-human headforms or mechanical phantoms.

Important precautions:

* Do not apply unvalidated mechanical excitation to human subjects.
* Do not exceed component voltage and current ratings.
* Verify amplifier and transducer compatibility.
* Monitor battery temperature.
* Use suitable battery protection.
* Prevent short circuits.
* Inspect wiring before powering the system.
* Disconnect power before changing hardware connections.

Any future human-subject investigation requires appropriate institutional ethics approval and safety evaluation.

---

# 12. Hardware Limitations

The current proposed architecture has several limitations that require further engineering work.

1. Sequential acquisition through the TCA9548A introduces timing differences between sensors.
2. Mechanical mounting influences acceleration measurements.
3. Excitation characteristics require independent calibration.
4. The PAM8403 frequency response must be verified for the intended transducer.
5. Power-supply noise may affect measurement quality.
6. Sensor orientation differences may influence spatial comparisons.
7. Mechanical coupling affects measured vibration amplitude.
8. Four-sensor integration requires systematic communication and timing validation.

These limitations should be considered during experimental analysis.

---

# 13. Future Hardware Improvements

Potential improvements include:

* Dedicated multi-sensor PCB development.
* Improved sensor mounting fixtures.
* Hardware-assisted synchronization.
* Dedicated excitation signal generation.
* Improved amplifier and transducer characterization.
* Separate regulated power rails.
* Improved electrical noise isolation.
* Automated hardware diagnostics.
* Battery monitoring.
* More robust enclosure design.

---

# 14. References

Technical component documentation:

* ESP32: https://docs.espressif.com/
* ADXL345: https://www.analog.com/en/products/adxl345.html
* TCA9548A: https://www.ti.com/product/TCA9548A
* PAM8403: https://www.diodes.com/part/view/PAM8403
* TP4056: Consult the manufacturer's datasheet for the exact module and IC variant.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Document:** Hardware Design and System Integration
**Category:** Embedded Biomedical Instrumentation
**Status:** Experimental Hardware Documentation
