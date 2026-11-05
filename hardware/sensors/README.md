# Multi-Sensor Acquisition System

## Cranial Mechanical Resonance Mapping (CMRM)

**Subsystem:** Multi-Sensor Acceleration Measurement
**Primary Sensor:** ADXL345 Digital Accelerometer
**Sensor Interface:** I²C
**Multiplexer:** TCA9548A
**Proposed Sensor Count:** Four
**Application:** Experimental Mechanical Vibration Characterization

---

# 1. Introduction

The sensor subsystem is responsible for measuring mechanical acceleration responses at multiple locations of a cranial headform.

CMRM uses ADXL345 three-axis digital accelerometers to acquire vibration measurements along three orthogonal axes.

Multiple sensors are distributed across the headform to investigate spatial differences in mechanical vibration responses.

The sensors communicate with the ESP32 through a TCA9548A I²C multiplexer.

The acquired measurements are transmitted to the processing subsystem for signal analysis and experimental visualization.

---

# 2. ADXL345 Accelerometer

## 2.1 Overview

The ADXL345 is a small, low-power, three-axis digital accelerometer manufactured by Analog Devices.

It measures acceleration along three perpendicular axes:

* X-axis.
* Y-axis.
* Z-axis.

The sensor communicates digitally through I²C or SPI.

In CMRM, the I²C interface is used for communication with the ESP32.

## 2.2 Technical Specifications

| Parameter         | Specification                                  |
| ----------------- | ---------------------------------------------- |
| Sensor            | ADXL345                                        |
| Manufacturer      | Analog Devices                                 |
| Measurement axes  | 3                                              |
| Measurement range | ±2g, ±4g, ±8g, ±16g                            |
| Resolution        | Up to 13 bits                                  |
| Digital interface | I²C / SPI                                      |
| Supply voltage    | Depends on sensor IC and breakout-board design |
| Output            | Digital acceleration measurements              |

Refer to the manufacturer's datasheet for complete electrical and operating specifications.

## 2.3 Measurement Principle

The ADXL345 uses a MEMS-based acceleration sensing mechanism.

Acceleration causes microscopic mechanical structures within the sensor to move.

The resulting changes in capacitance are converted into digital acceleration measurements.

The sensor provides acceleration values along three independent axes.

These measurements can be used to investigate vibration magnitude and frequency characteristics.

---

# 3. Sensor Configuration

The proposed CMRM architecture uses four ADXL345 modules.

Each sensor is assigned a unique identification number and measurement location.

| Sensor ID | Position       | Measurement Objective             |
| --------- | -------------- | --------------------------------- |
| S1        | Left Temporal  | Left temporal vibration response  |
| S2        | Right Temporal | Right temporal vibration response |
| S3        | Frontal        | Frontal vibration response        |
| S4        | Occipital      | Posterior vibration response      |

### Sensor Identification

Each sensor should maintain a consistent identification throughout:

* Hardware configuration.
* Firmware.
* Experimental datasets.
* Signal-processing algorithms.
* Desktop software.
* Experimental reports.

This prevents sensor-location confusion during data analysis.

---

# 4. Sensor Placement

Sensor placement is an important part of the experimental configuration.

The four sensors are positioned at selected locations on the cranial headform.

## 4.1 Left Temporal — S1

Measures mechanical acceleration at the left temporal region.

## 4.2 Right Temporal — S2

Measures mechanical acceleration at the right temporal region.

This sensor provides a corresponding measurement location for comparative analysis with S1.

## 4.3 Frontal — S3

Measures mechanical acceleration at the frontal region of the headform.

## 4.4 Occipital — S4

Measures mechanical acceleration at the posterior region of the headform.

### Placement Considerations

* Maintain consistent sensor orientation.
* Use stable mounting fixtures.
* Avoid loose mechanical connections.
* Document the exact mounting position.
* Maintain consistent mounting pressure.
* Avoid unnecessary cable movement.
* Record the headform configuration.

Sensor mounting conditions should remain consistent across repeated experiments.

---

# 5. TCA9548A I²C Multiplexer

## 5.1 Overview

The TCA9548A is an eight-channel I²C multiplexer.

It allows the ESP32 to communicate with multiple I²C devices through individually selectable channels.

In CMRM, it is used to manage communication with four ADXL345 sensor modules.

## 5.2 Channel Allocation

| TCA9548A Channel | Sensor ID | Location         |
| ---------------- | --------- | ---------------- |
| CH0              | S1        | Left Temporal    |
| CH1              | S2        | Right Temporal   |
| CH2              | S3        | Frontal          |
| CH3              | S4        | Occipital        |
| CH4–CH7          | Reserved  | Future expansion |

## 5.3 Communication Architecture

```text
              ESP32
                |
                |
              I²C
                |
                v
           TCA9548A
                |
       +--------+--------+
       |        |        |
      CH0      CH1      CH2      CH3
       |        |        |        |
       v        v        v        v
     ADXL345  ADXL345  ADXL345  ADXL345
       S1       S2       S3       S4
```

## 5.4 I²C Addressing

The TCA9548A commonly uses the default seven-bit address:

`0x70`

Its address can be changed through the address-selection pins.

The ADXL345 supports two selectable I²C addresses:

* `0x53`
* `0x1D`

The selected address depends on the SDO/ALT ADDRESS configuration.

The multiplexer allows multiple sensors with identical addresses to operate on separate channels.

---

# 6. Sensor Acquisition Methodology

The ESP32 accesses the four sensors sequentially.

### Acquisition Sequence

1. Select multiplexer channel 0.
2. Read acceleration from S1.
3. Record acquisition timestamp.
4. Select multiplexer channel 1.
5. Read acceleration from S2.
6. Record acquisition timestamp.
7. Select multiplexer channel 2.
8. Read acceleration from S3.
9. Record acquisition timestamp.
10. Select multiplexer channel 3.
11. Read acceleration from S4.
12. Record acquisition timestamp.
13. Repeat the acquisition cycle.

### Important Consideration

The TCA9548A provides channel selection, not simultaneous sensor sampling.

Therefore, the four sensor measurements must be treated as sequential observations.

The firmware should record individual timestamps and evaluate acquisition timing differences.

Do not assume that all four sensors share an identical measurement timestamp.

---

# 7. Acceleration Measurement

Each ADXL345 provides three acceleration components.

$$
a_x,\quad a_y,\quad a_z
$$

Where:

* \(a_x\): X-axis acceleration.
* \(a_y\): Y-axis acceleration.
* \(a_z\): Z-axis acceleration.

The resultant acceleration magnitude can be calculated as:

$$
a_r=\sqrt{a_x^2+a_y^2+a_z^2}
$$

The resultant magnitude represents the combined acceleration magnitude.

For vibration analysis, baseline and gravitational components must be considered.

## 7.1 Measurement Units

Acceleration measurements may be represented in:

* g
* m/s²

Conversion:

$$
1g=9.80665\ m/s^2
$$

Therefore:

$$
a_{m/s^2}=a_g\times9.80665
$$

All datasets should clearly identify the acceleration unit.

---

# 8. Sensor Configuration Parameters

The following parameters should be documented for every sensor.

| Parameter           | Description                     |
| ------------------- | ------------------------------- |
| Measurement range   | Selected acceleration range     |
| Output data rate    | Configured sensor sampling rate |
| Resolution          | Measurement resolution          |
| Sensor ID           | Unique sensor identifier        |
| I²C address         | Configured device address       |
| Multiplexer channel | Assigned channel                |
| Orientation         | Sensor axis orientation         |
| Calibration offset  | Measured calibration correction |
| Mounting position   | Physical sensor location        |

The selected output data rate must be compatible with the intended frequency-analysis range.

---

# 9. Sensor Calibration

Calibration is necessary to evaluate measurement accuracy and consistency.

Each sensor should be evaluated independently before system-level experiments.

## 9.1 Static Calibration

Static calibration can be performed using known gravitational acceleration orientations.

The sensor is placed in controlled orientations so that individual axes experience known gravity components.

Measurements can be used to evaluate:

* Offset errors.
* Axis sensitivity.
* Scale-factor differences.
* Axis orientation.

## 9.2 Offset Correction

A simple offset correction is:

$$
a_{corrected}=a_{measured}-b
$$

Where:

* \(a_{measured}\): Measured acceleration.
* \(b\): Estimated sensor offset.

## 9.3 Scale Correction

A calibrated measurement may be represented as:

$$
a_{calibrated}=s(a_{measured}-b)
$$

Where:

* \(s\): Calibration scale factor.
* \(b\): Estimated offset.

The calibration parameters should be stored separately for each sensor.

## 9.4 Dynamic Calibration

Dynamic calibration requires comparison against an appropriate reference measurement system.

It is particularly important when investigating vibration amplitude and frequency response.

Static calibration alone does not establish dynamic measurement accuracy.

---

# 10. Signal Quality Considerations

Sensor measurements may be affected by:

* Electrical interference.
* Mechanical disturbances.
* Sensor noise.
* Loose mounting.
* Cable movement.
* Power-supply fluctuations.
* Measurement saturation.
* Sampling irregularities.

The acquisition system should identify invalid measurements.

Suggested quality classifications:

| Status            | Description                                    |
| ----------------- | ---------------------------------------------- |
| GOOD              | Measurement satisfies defined quality criteria |
| WARNING           | Measurement has identifiable limitations       |
| INVALID           | Measurement cannot be reliably used            |
| INSUFFICIENT DATA | Insufficient valid samples                     |
| NOT AVAILABLE     | Measurement unavailable                        |

Quality thresholds should be established through testing rather than arbitrarily assigned.

---

# 11. Data Structure

A standardized measurement structure is recommended.

Example:

```json
{
  "experiment_id": "EXP_001",
  "sensor_id": "S1",
  "location": "Left Temporal",
  "timestamp_ms": 1250,
  "acceleration": {
    "x": 0.012,
    "y": 0.018,
    "z": 0.025
  },
  "unit": "g",
  "quality": "GOOD"
}
```

The numerical values are illustrative and do not represent actual experimental measurements.

Recommended data fields:

* Experiment identifier.
* Sensor identifier.
* Sensor location.
* Acquisition timestamp.
* X-axis acceleration.
* Y-axis acceleration.
* Z-axis acceleration.
* Measurement unit.
* Sampling configuration.
* Quality status.

---

# 12. Sensor Validation

Before experimental measurements, every sensor should undergo individual testing.

## 12.1 Communication Test

Verify:

* I²C device detection.
* Correct multiplexer channel.
* Valid sensor identification.
* Successful register communication.

## 12.2 Static Measurement Test

Place the sensor in a stationary position.

Check whether the measurements remain stable and correspond reasonably to the expected gravitational components.

## 12.3 Orientation Test

Rotate the sensor through known orientations.

Verify the expected changes in the X, Y and Z acceleration components.

## 12.4 Dynamic Measurement Test

Use an appropriate controlled mechanical reference to evaluate dynamic response.

## 12.5 Multi-Sensor Test

Operate all four sensors through the multiplexer.

Evaluate:

* Communication reliability.
* Acquisition timing.
* Measurement consistency.
* Channel selection.
* Data integrity.

---

# 13. Troubleshooting

| Problem                     | Possible Cause                      | Recommended Check                   |
| --------------------------- | ----------------------------------- | ----------------------------------- |
| Sensor not detected         | Wiring issue                        | Inspect SDA, SCL and power          |
| Incorrect I²C address       | Address configuration               | Check SDO/ALT ADDRESS               |
| Multiplexer channel failure | Channel-selection issue             | Verify TCA9548A configuration       |
| Unstable measurements       | Mechanical or electrical noise      | Inspect mounting and power          |
| Constant readings           | Sensor configuration issue          | Verify measurement mode             |
| Incorrect acceleration      | Calibration issue                   | Perform calibration                 |
| Missing measurements        | Communication interruption          | Check acquisition logs              |
| Different sensor responses  | Mounting or calibration differences | Compare under controlled conditions |

---

# 14. Limitations

The sensor subsystem has several experimental limitations.

1. Sensor readings are acquired sequentially.
2. Sensor mounting influences measured acceleration.
3. Sensor orientation affects axis-specific measurements.
4. Mechanical coupling influences vibration transmission.
5. The ADXL345 measurement bandwidth must be considered.
6. Sensor calibration is required for meaningful comparisons.
7. Dynamic response must be independently characterized.
8. The sensor's measurement range must be appropriate for the experiment.

---

# 15. Future Improvements

Potential improvements include:

* Dedicated sensor mounting fixtures.
* Custom sensor interface PCB.
* Improved acquisition synchronization.
* Automated sensor identification.
* Calibration management.
* Sensor health monitoring.
* Improved mechanical coupling consistency.
* Additional measurement locations.
* Independent reference instrumentation.

---

# 16. Technical References

1. Analog Devices — ADXL345 Product Documentation:
   https://www.analog.com/en/products/adxl345.html

2. Texas Instruments — TCA9548A Product Documentation:
   https://www.ti.com/product/TCA9548A

3. Espressif Systems — ESP32 Documentation:
   https://docs.espressif.com/

Consult the official datasheets for electrical specifications and operating limits.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)

**Subsystem:** Multi-Sensor Acquisition

**Primary Component:** ADXL345

**Communication Interface:** I²C

**Document Status:** Hardware Documentation
