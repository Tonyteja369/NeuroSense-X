# Embedded Firmware Architecture

## Cranial Mechanical Resonance Mapping (CMRM)

**Subsystem:** Embedded Firmware
**Controller:** ESP32
**Programming Environment:** Arduino IDE / ESP-IDF
**Sensor Interface:** I²C
**Communication:** USB Serial
**Application:** Multi-Sensor Acquisition and Experimental Control

---

# 1. Introduction

The firmware subsystem is responsible for controlling and coordinating the embedded hardware components of the Cranial Mechanical Resonance Mapping (CMRM) platform.

It runs on the ESP32 microcontroller and provides the communication interface between the sensor acquisition system, mechanical excitation subsystem and desktop application.

The firmware manages sensor initialization, I²C communication, measurement acquisition, experimental parameters, data transmission and system error handling.

The primary objective is to establish a reliable and modular embedded software architecture for experimental mechanical vibration measurements.

---

# 2. Firmware Objectives

The firmware is designed to:

1. Initialize the ESP32 hardware.
2. Configure I²C communication.
3. Detect the TCA9548A multiplexer.
4. Initialize individual ADXL345 sensors.
5. Coordinate multi-sensor acquisition.
6. Generate timestamps for measurements.
7. Configure experimental parameters.
8. Generate excitation-control signals.
9. Transmit measurements to desktop software.
10. Monitor hardware communication.
11. Handle acquisition errors.
12. Maintain safe operating states.

---

# 3. Firmware Architecture

The firmware follows a modular architecture.

```text
             ESP32 FIRMWARE
                    |
          INITIALIZATION MANAGER
                    |
          +---------+---------+
          |         |         |
          v         v         v
        I²C      SENSOR    EXCITATION
      MANAGER    MANAGER   CONTROLLER
          |         |         |
          v         v         v
       TCA9548A   ADXL345   PWM OUTPUT
          |         |         |
          +---------+---------+
                    |
                    v
           ACQUISITION MANAGER
                    |
                    v
           DATA VALIDATION
                    |
                    v
          COMMUNICATION MANAGER
                    |
                    v
             USB SERIAL
                    |
                    v
           DESKTOP APPLICATION
```

---

# 4. Firmware Modules

## 4.1 Main Controller

Responsible for:

* Firmware initialization.
* Module coordination.
* System-state management.
* Main execution loop.
* Error handling.

## 4.2 I²C Manager

Responsible for:

* I²C initialization.
* TCA9548A communication.
* Channel selection.
* I²C error detection.
* Bus communication management.

## 4.3 Sensor Manager

Responsible for:

* ADXL345 initialization.
* Sensor identification.
* Measurement configuration.
* Acceleration acquisition.
* Sensor availability monitoring.

## 4.4 Acquisition Manager

Responsible for:

* Measurement scheduling.
* Sensor polling.
* Timestamp generation.
* Measurement buffering.
* Data validation.

## 4.5 Excitation Controller

Responsible for:

* Excitation configuration.
* PWM signal generation.
* Frequency control.
* Excitation duration.
* Safe activation and deactivation.

The actual electrical waveform must be verified independently.

## 4.6 Communication Manager

Responsible for:

* USB serial communication.
* Measurement transmission.
* Command reception.
* Packet formatting.
* Communication error reporting.

---

# 5. Hardware Configuration

The firmware communicates with the following hardware components.

| Component  | Interface            | Function                   |
| ---------- | -------------------- | -------------------------- |
| ESP32      | Main controller      | System coordination        |
| TCA9548A   | I²C                  | Sensor channel selection   |
| ADXL345 S1 | I²C                  | Left temporal measurement  |
| ADXL345 S2 | I²C                  | Right temporal measurement |
| ADXL345 S3 | I²C                  | Frontal measurement        |
| ADXL345 S4 | I²C                  | Occipital measurement      |
| PAM8403    | Excitation interface | Signal amplification       |
| USB        | Serial               | Desktop communication      |

GPIO assignments must match the actual assembled hardware.

---

# 6. Sensor Identification and Channel Mapping

The proposed sensor mapping is:

| Sensor | Multiplexer Channel | Location       |
| ------ | ------------------- | -------------- |
| S1     | CH0                 | Left Temporal  |
| S2     | CH1                 | Right Temporal |
| S3     | CH2                 | Frontal        |
| S4     | CH3                 | Occipital      |

The firmware selects one channel at a time and communicates with the corresponding sensor.

Sensor initialization failures should be recorded individually.

A missing sensor must not be represented as a valid zero-valued measurement.

---

# 7. Acquisition Workflow

The proposed acquisition sequence is:

```text
START
  |
  v
INITIALIZE ESP32
  |
  v
INITIALIZE I²C
  |
  v
DETECT TCA9548A
  |
  v
INITIALIZE SENSOR CHANNELS
  |
  v
VERIFY SENSOR AVAILABILITY
  |
  v
START EXPERIMENT
  |
  v
SELECT SENSOR CHANNEL
  |
  v
READ ACCELERATION
  |
  v
GENERATE TIMESTAMP
  |
  v
VALIDATE MEASUREMENT
  |
  v
STORE IN BUFFER
  |
  v
SELECT NEXT SENSOR
  |
  v
TRANSMIT DATA
  |
  v
REPEAT
```

The acquisition sequence must account for the sequential operation of the TCA9548A.

Individual sensor timestamps should be retained.

---

# 8. Measurement Data Structure

Each measurement should contain:

| Field          | Description                   |
| -------------- | ----------------------------- |
| Experiment ID  | Current experiment identifier |
| Sensor ID      | Sensor identification         |
| Timestamp      | Acquisition time              |
| Acceleration X | X-axis acceleration           |
| Acceleration Y | Y-axis acceleration           |
| Acceleration Z | Z-axis acceleration           |
| Unit           | Acceleration measurement unit |
| Quality        | Measurement validity          |

### Example JSON Packet

```json
{
  "device": "CMRM_ESP32",
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
  "status": "VALID"
}
```

The values above are illustrative examples, not experimental measurements.

---

# 9. Communication Protocol

The ESP32 communicates with the desktop application through USB serial.

A newline-delimited JSON protocol is proposed for initial implementation.

### Communication Requirements

* Consistent message structure.
* Sensor identification.
* Experiment identification.
* Timestamps.
* Measurement units.
* Error reporting.
* Input validation.

### Proposed Commands

| Command           | Function                        |
| ----------------- | ------------------------------- |
| STATUS            | Request device status           |
| SENSOR_SCAN       | Check sensor availability       |
| START_ACQUISITION | Start measurement acquisition   |
| STOP_ACQUISITION  | Stop acquisition                |
| CONFIGURE         | Configure experiment parameters |
| GET_CONFIG        | Retrieve configuration          |
| EXCITATION_STOP   | Stop excitation                 |

These commands describe a proposed protocol. They should not be treated as implemented until firmware support exists.

---

# 10. Experimental State Management

The firmware should use explicit operating states.

| State        | Description                             |
| ------------ | --------------------------------------- |
| BOOT         | Initial startup                         |
| INITIALIZING | Hardware initialization                 |
| IDLE         | Waiting for experiment                  |
| READY        | Hardware checks passed                  |
| ACQUIRING    | Collecting measurements                 |
| STOPPING     | Ending acquisition                      |
| ERROR        | Hardware or communication failure       |
| SAFE         | Excitation disabled and system inactive |

The firmware should reject commands that are invalid for the current operating state.

On startup, reset or communication failure, the excitation output should default to an inactive state.

---

# 11. Timestamp Management

Timestamping is important for experimental reproducibility.

Each measurement should include its acquisition time.

The firmware can use a monotonic timer to record elapsed time.

Example:

```cpp
uint32_t timestamp = millis();
```

For longer experiments, rollover handling and timer resolution should be considered.

The timestamp should represent the measurement acquisition time rather than the time at which the desktop application receives the packet.

---

# 12. Error Handling

The firmware should detect and report:

* I²C communication failures.
* Multiplexer detection failures.
* Sensor initialization errors.
* Invalid acceleration readings.
* Serial communication failures.
* Invalid commands.
* Acquisition interruptions.
* Unexpected system resets.

Example error structure:

```json
{
  "device": "CMRM_ESP32",
  "status": "ERROR",
  "module": "SENSOR_MANAGER",
  "sensor_id": "S2",
  "error": "SENSOR_NOT_RESPONDING"
}
```

The firmware should preserve diagnostic information without exposing sensitive configuration data.

---

# 13. Safety and Excitation Control

The excitation controller must implement appropriate safeguards.

Recommended behaviour:

1. Initialize excitation output in an inactive state.
2. Require explicit authorization before activation.
3. Validate excitation parameters.
4. Enforce configured operating limits.
5. Stop excitation when an experiment ends.
6. Disable excitation following a critical fault.
7. Reject invalid activation commands.

The PWM output must not be assumed to generate a calibrated mechanical excitation signal.

Amplifier compatibility, electrical output and transducer response require independent characterization.

---

# 14. Firmware Development Environment

Potential development environments:

* Arduino IDE.
* PlatformIO.
* ESP-IDF.

Potential libraries:

| Library                | Purpose                     |
| ---------------------- | --------------------------- |
| Wire                   | I²C communication           |
| Arduino core for ESP32 | ESP32 development           |
| ADXL345 driver         | Accelerometer communication |
| ArduinoJson            | JSON serialization          |

The actual dependency list should reflect the libraries used by the implementation.

---

# 15. Testing and Validation

## Controller Testing

* [ ] Verify ESP32 startup.
* [ ] Verify serial communication.
* [ ] Verify GPIO configuration.
* [ ] Verify watchdog behaviour.

## I²C Testing

* [ ] Initialize I²C.
* [ ] Detect TCA9548A.
* [ ] Verify channel selection.
* [ ] Check communication errors.

## Sensor Testing

* [ ] Initialize S1.
* [ ] Initialize S2.
* [ ] Initialize S3.
* [ ] Initialize S4.
* [ ] Verify acceleration readings.
* [ ] Validate measurement units.

## Acquisition Testing

* [ ] Verify timestamps.
* [ ] Verify acquisition scheduling.
* [ ] Measure channel-switching delays.
* [ ] Evaluate missing samples.
* [ ] Validate data transmission.

## Excitation Testing

* [ ] Verify inactive startup state.
* [ ] Validate activation commands.
* [ ] Verify shutdown behaviour.
* [ ] Characterize output waveform.

## Integration Testing

* [ ] Connect desktop application.
* [ ] Verify packet parsing.
* [ ] Test experiment initiation.
* [ ] Test experiment termination.
* [ ] Test communication failures.

---

# 16. Future Improvements

Potential firmware improvements include:

* Improved acquisition scheduling.
* Hardware-assisted synchronization.
* Robust communication framing.
* Watchdog-based fault recovery.
* Non-blocking sensor polling.
* Improved data buffering.
* Firmware update support.
* Battery monitoring.
* Hardware diagnostics.
* Secure firmware configuration.

---

# 17. Security Considerations

Firmware security should follow the repository's SECURITY.md policy.

Important measures:

* Avoid hardcoded credentials.
* Validate incoming commands.
* Restrict unnecessary wireless services.
* Protect configuration data.
* Evaluate Secure Boot where supported.
* Evaluate Flash Encryption where supported.
* Maintain safe default operating states.

---

# 18. References

1. Espressif Systems — ESP32 Documentation: https://docs.espressif.com/
2. Analog Devices — ADXL345 Documentation: https://www.analog.com/en/products/adxl345.html
3. Texas Instruments — TCA9548A Documentation: https://www.ti.com/product/TCA9548A

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)

**Subsystem:** Embedded Firmware

**Controller:** ESP32

**Document:** Firmware Architecture and Development

**Status:** Development Documentation
