# Power Management System

## Cranial Mechanical Resonance Mapping (CMRM)

**Subsystem:** Power Management and Distribution
**Power Source:** Rechargeable Lithium-Ion Battery
**Charging Controller:** TP4056
**Voltage Regulation:** 5V Regulation Stage
**Application:** Embedded Multi-Sensor Experimental Platform

---

# 1. Introduction

The power-management subsystem provides regulated electrical power to the different hardware components of the Cranial Mechanical Resonance Mapping (CMRM) platform.

The system is designed around a rechargeable lithium-ion battery, a TP4056 charging module and a voltage-regulation stage.

The power architecture supplies the embedded controller, sensor acquisition circuitry and mechanical excitation electronics.

Reliable power distribution is important because electrical noise, voltage fluctuations and inadequate current delivery can affect sensor measurements and experimental repeatability.

The proposed power-management architecture consists of:

1. Rechargeable lithium-ion battery.
2. TP4056 charging module.
3. Voltage regulation stage.
4. ESP32 power supply.
5. Sensor power distribution.
6. Excitation amplifier supply.

---

# 2. Power Architecture

The proposed power architecture is:

```text
     3.7V Li-ion Battery
             |
             v
       TP4056 Module
             |
             v
     Battery Protection
       (if required)
             |
             v
     Voltage Regulation
             |
        +----+-----+
        |          |
        v          v
    ESP32      5V Power Rail
                   |
             +-----+------+
             |            |
             v            v
          Sensors      Amplifier
```

**Important:** This is a conceptual power architecture. The final electrical connections depend on the actual regulator, TP4056 module variant and component supply requirements.

The TP4056 is a battery charging controller, not a voltage regulator.

---

# 3. Lithium-Ion Battery

## 3.1 Overview

The proposed system uses a rechargeable single-cell lithium-ion battery.

The battery acts as the primary energy source for portable operation.

A typical single-cell lithium-ion battery has:

| Parameter             | Typical Value |
| --------------------- | ------------- |
| Nominal voltage       | 3.6–3.7V      |
| Fully charged voltage | 4.2V          |
| Chemistry             | Lithium-ion   |
| Rechargeability       | Yes           |

Actual voltage limits depend on the selected battery chemistry and manufacturer's specifications.

## 3.2 Battery Selection Considerations

Battery selection should consider:

* Capacity.
* Maximum discharge current.
* Continuous current rating.
* Internal resistance.
* Charging requirements.
* Protection circuitry.
* Operating temperature.
* Expected operating duration.

The battery must support the combined current requirements of the embedded controller, sensors and excitation subsystem.

## 3.3 Battery Capacity

Battery capacity is commonly expressed in milliampere-hours (mAh).

An approximate operating-time calculation is:

$$
T=\frac{C}{I}
$$

Where:

* \(T\): Approximate operating time in hours.
* \(C\): Battery capacity in mAh.
* \(I\): Average load current in mA.

This is an idealized estimate.

Actual operating time depends on regulator efficiency, battery characteristics, load variation and discharge limits.

---

# 4. TP4056 Charging Module

## 4.1 Overview

The TP4056 is a linear constant-current/constant-voltage charging controller designed for single-cell lithium-ion batteries.

Its purpose is to manage the battery charging process.

It is not designed to convert the battery output into a regulated 5V supply.

## 4.2 Primary Functions

* Controlled battery charging.
* Constant-current charging.
* Constant-voltage charging.
* Charge termination.
* Charging status indication.

## 4.3 Typical Module Connections

| Terminal | Function                               |
| -------- | -------------------------------------- |
| IN+      | Charging input positive                |
| IN-      | Charging input ground                  |
| B+       | Battery positive                       |
| B-       | Battery negative                       |
| OUT+     | Protected output positive, if provided |
| OUT-     | Protected output negative, if provided |

Terminal availability and protection circuitry depend on the specific module.

## 4.4 Important Considerations

Not every TP4056 breakout module includes battery protection.

If protection is not integrated, an appropriate battery protection circuit must be provided separately.

Charging current must be configured according to the battery manufacturer's specifications.

The charging circuit should not be assumed to support unrestricted simultaneous charging and system operation.

---

# 5. Voltage Regulation

## 5.1 Purpose

The voltage-regulation stage converts the battery supply into the voltage rails required by the electronic subsystems.

The proposed architecture includes a 5V regulation stage.

## 5.2 Regulation Requirements

The regulator must be selected based on:

* Input voltage range.
* Output voltage.
* Maximum output current.
* Conversion efficiency.
* Thermal dissipation.
* Output ripple.
* Transient response.

A battery voltage of approximately 3.7V cannot directly provide a stable 5V output.

A suitable boost converter or another appropriate power-conversion architecture is required.

## 5.3 ESP32 Supply

The ESP32 chip operates with a 3.3V supply.

However, many development boards accept 5V through their designated USB or 5V/VIN input.

The exact supply connection depends on the development-board design.

Do not connect 5V directly to an ESP32 3.3V pin.

## 5.4 Sensor Supply

The ADXL345 IC operates at low voltage.

The supply requirements of the complete breakout module depend on its onboard regulator and level-shifting circuitry.

Verify the exact module specification before supplying 3.3V or 5V.

## 5.5 Amplifier Supply

The PAM8403 amplifier requires a supply within its specified operating range.

The amplifier supply must support the required current without causing excessive voltage fluctuations.

The regulator should be sized according to the actual amplifier load.

---

# 6. Power Distribution Architecture

The proposed system contains three primary electrical domains.

### 6.1 Control Domain

Components:

* ESP32.
* Communication circuitry.
* I²C interface.
* Sensor control.

### 6.2 Measurement Domain

Components:

* ADXL345 sensors.
* TCA9548A multiplexer.
* Associated measurement electronics.

This domain should be protected against unnecessary electrical interference.

### 6.3 Excitation Domain

Components:

* PAM8403 amplifier.
* Bone-conduction transducer.
* Excitation circuitry.

The excitation subsystem can introduce electrical noise and supply-current fluctuations.

Its power distribution should be designed carefully to reduce interference with the measurement electronics.

---

# 7. Grounding and Electrical Noise

Proper grounding is essential for reliable sensor measurements.

The system should establish an appropriate common electrical reference for the ESP32, sensors and amplifier interfaces.

However, grounding topology must account for current paths.

## 7.1 Grounding Considerations

* Maintain suitable common signal references.
* Avoid unnecessary ground loops.
* Keep high-current amplifier return paths away from sensitive sensor paths where practical.
* Use appropriate PCB ground-plane design.
* Ensure reliable connector contacts.
* Minimize long, poorly routed signal wires.

## 7.2 Electrical Noise Sources

Potential noise sources include:

* Switching regulators.
* Class-D audio amplifiers.
* USB power supplies.
* PWM switching.
* Long electrical connections.
* Poor grounding.
* Battery voltage fluctuations.

## 7.3 Noise Reduction

Recommended practices:

* Appropriate decoupling capacitors.
* Suitable power filtering.
* Proper PCB layout.
* Separate high-current and low-level signal routing.
* Short sensor connections.
* Stable regulated supply rails.

Any filtering or grounding improvement should be verified through measurements.

---

# 8. Power Consumption Analysis

The complete system's current consumption should be measured experimentally.

A power budget should include:

| Component       | Supply Rail      | Current        |
| --------------- | ---------------- | -------------- |
| ESP32           | Board-dependent  | To be measured |
| ADXL345 S1      | Sensor supply    | To be measured |
| ADXL345 S2      | Sensor supply    | To be measured |
| ADXL345 S3      | Sensor supply    | To be measured |
| ADXL345 S4      | Sensor supply    | To be measured |
| TCA9548A        | Sensor supply    | To be measured |
| PAM8403         | Amplifier supply | Load-dependent |
| Other circuitry | As required      | To be measured |

The total current can be estimated as:

$$
I_{total}=\sum_{i=1}^{n}I_i
$$

For different voltage rails, power should be calculated using:

$$
P=VI
$$

The total system power is the sum of the power consumed by individual subsystems.

## 8.1 Battery Runtime Estimation

An approximate runtime calculation is:

$$
T\approx\frac{E_{battery}\times\eta}{P_{load}}
$$

Where:

* \(E_{battery}\): Usable battery energy in Wh.
* \(\eta\): Power-conversion efficiency.
* \(P_{load}\): Average system power in W.

This estimate should be refined using actual measured load conditions.

---

# 9. Power Management During Experiments

The firmware and hardware should support safe experimental operation.

Recommended operating sequence:

1. Verify battery condition.
2. Verify supply voltage.
3. Initialize the ESP32.
4. Initialize sensor communication.
5. Verify sensor availability.
6. Enable experimental acquisition.
7. Activate excitation only when authorized.
8. Monitor power stability.
9. Stop excitation after the experiment.
10. Save experimental measurements.
11. Enter a safe shutdown state when required.

The excitation subsystem should default to an inactive state during initialization and after fault conditions.

---

# 10. Electrical Safety

Lithium-ion batteries require appropriate handling and protection.

Recommended precautions:

* Use a compatible battery charger.
* Verify charging current.
* Avoid reverse polarity.
* Prevent short circuits.
* Avoid excessive discharge.
* Monitor battery temperature.
* Use suitable protection circuitry.
* Avoid damaged or swollen batteries.
* Verify regulator temperature.
* Inspect wiring before powering the system.

Do not operate the battery outside its manufacturer's specified limits.

---

# 11. Power Validation

The power subsystem should undergo systematic testing.

## 11.1 Battery Validation

* [ ] Verify battery voltage.
* [ ] Verify battery polarity.
* [ ] Confirm battery capacity rating.
* [ ] Verify maximum discharge current.
* [ ] Inspect physical battery condition.

## 11.2 Charging Validation

* [ ] Verify TP4056 input supply.
* [ ] Verify charging operation.
* [ ] Verify charging termination.
* [ ] Verify battery protection.
* [ ] Check module temperature.

## 11.3 Voltage Regulation Validation

* [ ] Measure regulator input voltage.
* [ ] Measure regulated output voltage.
* [ ] Measure output ripple.
* [ ] Verify current capability.
* [ ] Check thermal behaviour.

## 11.4 System-Level Validation

* [ ] Verify ESP32 supply stability.
* [ ] Verify sensor supply stability.
* [ ] Verify amplifier supply.
* [ ] Measure total current consumption.
* [ ] Evaluate electrical noise.
* [ ] Test system under maximum expected load.

---

# 12. Troubleshooting

| Problem                         | Possible Cause              | Recommended Action                   |
| ------------------------------- | --------------------------- | ------------------------------------ |
| ESP32 resets unexpectedly       | Supply instability          | Measure voltage during operation     |
| Sensors disconnect              | Power fluctuations          | Inspect sensor supply                |
| Amplifier noise                 | Poor grounding              | Review power and ground routing      |
| Regulator overheats             | Excessive load              | Verify current rating                |
| Battery drains quickly          | High system consumption     | Measure subsystem currents           |
| Charging failure                | Incorrect wiring            | Verify TP4056 connections            |
| Unstable sensor readings        | Electrical interference     | Investigate switching noise          |
| Voltage drops during excitation | Insufficient power capacity | Review regulator and battery ratings |

---

# 13. Future Improvements

Potential power-management improvements include:

* Dedicated power-management PCB.
* Battery voltage monitoring.
* Battery state-of-charge estimation.
* Improved regulator efficiency.
* Separate measurement and excitation power domains.
* Overcurrent protection.
* Thermal monitoring.
* Power-failure detection.
* Low-power firmware operation.
* Improved battery runtime estimation.

---

# 14. References

1. TP4056 — Consult the manufacturer's datasheet for the selected charging IC.
2. ESP32 — https://docs.espressif.com/
3. ADXL345 — https://www.analog.com/en/products/adxl345.html
4. PAM8403 — https://www.diodes.com/part/view/PAM8403

Refer to the exact component datasheets and breakout-board documentation before finalizing the electrical design.

---

# 15. Research Disclaimer

The power-management architecture described here represents an experimental engineering design.

Actual electrical ratings, regulator configurations, charging parameters and battery performance must be established through hardware verification.

No component should be operated outside its specified electrical limits.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Subsystem:** Power Management
**Document:** Power Architecture and Electrical Integration
**Status:** Development Documentation
