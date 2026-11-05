
# NEUROSENSE-X

### Cranial Mechanical Resonance Mapping (CMRM)

**An Embedded Multi-Sensor Platform for Mechanical Vibration Characterization and Frequency-Response Analysis**

<br>

![Research Prototype](https://img.shields.io/badge/STATUS-RESEARCH%20PROTOTYPE-orange?style=for-the-badge)
![Version](https://img.shields.io/badge/VERSION-0.1.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/LICENSE-MIT-green?style=for-the-badge)
![Platform](https://img.shields.io/badge/PLATFORM-ESP32-black?style=for-the-badge)

<br>

![GitHub Stars](https://img.shields.io/github/stars/Tonyteja369/NeuroSense-X?style=social)
![GitHub Forks](https://img.shields.io/github/forks/Tonyteja369/NeuroSense-X?style=social)
![GitHub Issues](https://img.shields.io/github/issues/Tonyteja369/NeuroSense-X)
![Last Commit](https://img.shields.io/github/last-commit/Tonyteja369/NeuroSense-X)

<br>

**Biomedical Engineering · Embedded Systems · Signal Processing · Experimental Research**

<br>

*Conceptualized, founded and led by KATIKIREDDY THARUN*

</div>

---


<p align="center">
  <img src="docs/architecture.png" alt="CMRM System Architecture" width="100%">
</p>

<p align="center">
  <strong>Biomedical Engineering | Embedded Systems | Signal Processing | Experimental Research</strong>
</p>


## 1. Introduction

Cranial Mechanical Resonance Mapping (CMRM) is an experimental biomedical engineering project designed to investigate mechanical vibration characteristics across multiple locations of a cranial headform.

The proposed platform integrates embedded electronics, multi-sensor acceleration measurement, controlled mechanical excitation, digital signal processing and computational visualization.

The system uses an ESP32 microcontroller as its central processing and communication unit, multiple ADXL345 accelerometers for vibration measurement, and a TCA9548A I²C multiplexer for sensor communication management.

The acquired acceleration signals are intended to be processed to investigate frequency-dependent mechanical responses, vibration amplitude variations, spatial response characteristics and potential resonance behaviour.

The project also explores an integrated desktop software environment for experimental monitoring, signal visualization, data management and AI-assisted pattern analysis.

**CMRM is an experimental engineering research prototype. It is not a clinically validated diagnostic device.**

---

## Project Documentation

| Document | Description |
|---|---|
| [Hardware Design](docs/hardware-design.md) | Hardware architecture |
| [Signal Processing](docs/signal-processing.md) | Mathematical framework |
| [Firmware](firmware/README.md) | Embedded development |
| [Software](software/README.md) | Desktop application |
| [Datasets](datasets/README.md) | Dataset management |
| [Research](research/README.md) | Research methodology |
| [Contributing](CONTRIBUTING.md) | Contribution guidelines |
| [Security](SECURITY.md) | Security policy |

## 2. Project Vision

The central vision of CMRM is to develop an experimental platform that combines embedded sensing and computational analysis to characterize mechanical vibration responses in cranial models.

The project explores the following engineering domains:

* Biomedical instrumentation.
* Mechanical vibration analysis.
* Embedded systems.
* Multi-sensor data acquisition.
* Digital signal processing.
* Frequency-response characterization.
* Artificial intelligence.
* Experimental biomechanics.

---

## 3. Project Objectives

1. Design a multi-sensor embedded measurement platform.
2. Integrate multiple ADXL345 accelerometers.
3. Implement sensor communication through the TCA9548A multiplexer.
4. Develop an ESP32-based experimental controller.
5. Implement controlled mechanical excitation.
6. Acquire and record acceleration signals.
7. Develop digital signal-processing algorithms.
8. Investigate frequency-response characteristics.
9. Generate experimental Mechanical Resonance Profiles.
10. Develop an interactive software visualization platform.
11. Explore AI-assisted mechanical response analysis.
12. Establish secure and reproducible experimental data management.

---

## 4. System Architecture

The proposed system is organized into six major subsystems.

### 4.1 Power Management

Responsible for supplying regulated electrical power to the embedded controller, sensors and excitation circuitry.

Components include:

* Rechargeable lithium-ion battery.
* TP4056 charging module.
* Voltage regulation circuitry.
* Power distribution.

### 4.2 Embedded Control

The ESP32 acts as the central embedded controller.

Responsibilities:

* Sensor communication.
* I²C management.
* PWM signal generation.
* Experimental control.
* Data acquisition coordination.
* Serial communication.
* Optional wireless communication.

### 4.3 Mechanical Excitation

The excitation subsystem is designed to generate controlled mechanical vibrations using an electronic excitation circuit and a bone-conduction transducer.

The excitation parameters include frequency, amplitude, waveform and duration.

Actual mechanical output must be independently characterized.

### 4.4 Multi-Sensor Acquisition

The proposed configuration uses four ADXL345 accelerometers distributed across a cranial headform.

| Sensor | Position       |
| ------ | -------------- |
| S1     | Left temporal  |
| S2     | Right temporal |
| S3     | Frontal        |
| S4     | Occipital      |

The TCA9548A multiplexer manages communication between the ESP32 and the sensor modules.

Sensor readings require timestamping and acquisition-skew characterization because multiplexer channels are accessed sequentially.

### 4.5 Signal Processing

The signal-processing subsystem performs:

* Signal validation.
* Baseline correction.
* Noise filtering.
* FFT analysis.
* Frequency-spectrum extraction.
* Feature extraction.
* Frequency-response analysis.
* Mechanical response characterization.

### 4.6 AI Analysis

The proposed AI subsystem explores:

* Pattern recognition.
* Mechanical response comparison.
* Feature classification.
* Resonance candidate detection.
* Experimental data analysis.
* Explainable analytical outputs.

---

## 5. Hardware Components

| Component                  | Function                            |
| -------------------------- | ----------------------------------- |
| ESP32                      | Embedded controller                 |
| ADXL345                    | Three-axis acceleration measurement |
| TCA9548A                   | I²C multiplexer                     |
| PAM8403                    | Audio signal amplification          |
| Bone-conduction transducer | Mechanical excitation               |
| TP4056                     | Battery charging                    |
| Lithium-ion battery        | Portable power                      |
| Voltage regulator          | Power regulation                    |
| PCB / Breadboard           | Circuit integration                 |

Refer to `docs/hardware-design.md` for detailed hardware documentation.

---

## 6. Software Architecture

The desktop application is intended to provide a unified interface for experimental operation and analysis.

### Proposed Modules

1. Overview Dashboard.
2. Hardware Connection.
3. Experiment Management.
4. Live Sensor Monitoring.
5. Signal Processing.
6. Frequency Sweep.
7. Resonance Analysis.
8. Mechanical Resonance Mapping.
9. Sensor Comparison.
10. AI Analysis.
11. Report Generation.
12. Calibration.
13. Firmware and Communication.
14. Settings and Security.

The implementation status of each module should be documented separately.

---

## 7. Mechanical Resonance Profile (MRP)

The Mechanical Resonance Profile is the proposed consolidated representation of the experimentally measured mechanical response.

Potential parameters include:

* Candidate resonance frequencies.
* Vibration amplitude.
* Frequency-response characteristics.
* Spatial vibration distribution.
* Mechanical symmetry.
* Damping characteristics.
* Measurement repeatability.

These parameters require appropriate calibration and experimental validation.

---

## 8. Signal Processing

The proposed signal-processing pipeline includes:

1. Data acquisition.
2. Signal preprocessing.
3. Noise filtering.
4. Timestamp alignment.
5. FFT transformation.
6. Frequency-domain analysis.
7. Feature extraction.
8. Mechanical response analysis.
9. Visualization.
10. Experimental reporting.

Refer to `docs/signal-processing.md` for mathematical foundations and methodology.

---

## 9. Repository Structure

```text
Cranial-Mechanical-Resonance-Mapping/
│
├── README.md
├── SECURITY.md
├── LICENSE
│
├── docs/
│   ├── architecture.svg
│   ├── architecture.png
│   ├── hardware-design.md
│   └── signal-processing.md
│
├── hardware/
│   ├── esp32/
│   ├── sensors/
│   └── power-management/
│
├── firmware/
│
├── software/
│
├── signal-processing/
│
├── datasets/
│   └── sample_data/
│
└── research/
```

---

## 10. Development Status

The following table represents the proposed development areas. Update the status according to actual implementation and testing.

| Module                       | Status              |
| ---------------------------- | ------------------- |
| ESP32 controller             | Prototype           |
| ADXL345 integration          | Prototype           |
| TCA9548A integration         | Prototype           |
| Power management             | Under validation    |
| Mechanical excitation        | Experimental        |
| Multi-sensor synchronization | Requires validation |
| Firmware                     | Under development   |
| Desktop application          | Prototype           |
| FFT analysis                 | Under development   |
| Frequency-response analysis  | Requires validation |
| Mechanical Resonance Profile | Experimental        |
| AI analysis                  | Proposed            |
| Clinical validation          | Not performed       |

---

## 11. Experimental Validation

The project requires systematic validation.

### Hardware Validation

* Verify electrical connections.
* Validate sensor communication.
* Check power stability.
* Characterize excitation output.

### Signal Validation

* Verify sampling frequency.
* Evaluate acquisition timing.
* Validate filtering.
* Check FFT calculations.

### Mechanical Validation

* Use a suitable mechanical headform or phantom.
* Apply controlled excitation.
* Measure mechanical responses.
* Compare results with an independent reference instrument.
* Evaluate repeatability.

### Software Validation

* Test real-time acquisition.
* Verify signal visualization.
* Validate data exports.
* Test experiment management.

---

## 12. Security and Data Privacy

The project follows a privacy-conscious engineering approach.

Security considerations include:

* Secure configuration management.
* No hardcoded credentials.
* Encrypted sensitive data storage.
* Authenticated communication.
* Restricted access to experimental data.
* Secure firmware deployment.
* Controlled data exports.
* Protection of participant information.

Refer to `SECURITY.md`.

---

## 13. Limitations

Current and anticipated limitations include:

* Sequential sensor acquisition through the I²C multiplexer.
* Sensor mounting variability.
* Mechanical coupling effects.
* Electrical interference.
* Excitation calibration requirements.
* Measurement uncertainty.
* Limited experimentally validated datasets.
* Need for independent verification of analytical algorithms.

---

## 14. Future Development

Potential development directions:

* Improved multi-sensor synchronization.
* Custom PCB development.
* Improved excitation electronics.
* Advanced signal-processing algorithms.
* Spatial vibration visualization.
* Automated experimental analysis.
* Explainable AI integration.
* Improved data security.
* Reproducible experimental validation.

---

## 15. Research and Safety Disclaimer

CMRM is intended for experimental engineering research using appropriate non-human headforms or mechanical phantoms.

It must not be used for clinical diagnosis, treatment decisions or medical screening.

Any future human-subject investigation requires appropriate institutional ethics approval, informed consent and safety evaluation.

Experimental observations must not be presented as clinically established findings.

---

## 16. Documentation

| Document                    | Description                          |
| --------------------------- | ------------------------------------ |
| `docs/architecture.svg`     | Scalable system architecture         |
| `docs/architecture.png`     | Architecture preview                 |
| `docs/hardware-design.md`   | Hardware engineering                 |
| `docs/signal-processing.md` | Signal-processing methodology        |
| `SECURITY.md`               | Security and vulnerability reporting |

---

## 17. Research Identity

**Project:** Cranial Mechanical Resonance Mapping (CMRM)

**Domain:** Biomedical Engineering

**Research Areas:** Embedded Instrumentation, Mechanical Vibration Analysis, Multi-Sensor Acquisition and Digital Signal Processing.

**Development Approach:** Experimental, modular and validation-oriented.

## Navigation

| Section | Description |
|---|---|
| [Overview](#overview) | Project introduction |
| [Architecture](#system-architecture) | Hardware and software design |
| [Research Objectives](#research-objectives) | Research direction |
| [Hardware](#hardware-architecture) | Embedded components |
| [Signal Processing](#signal-processing) | Mathematical framework |
| [MRP](#mechanical-resonance-profile) | Analytical framework |
| [Roadmap](ROADMAP.md) | Development milestones |
| [Documentation](#project-documentation) | Technical documents |
| [Contributing](CONTRIBUTING.md) | Join development |

## Technology Stack

| Category | Technologies |
|---|---|
| Embedded Controller | ESP32 |
| Accelerometer | ADXL345 |
| Communication | I2C / UART |
| Multiplexer | TCA9548A |
| Signal Processing | FFT / Digital Filtering |
| Excitation | PAM8403 / Bone-Conduction Transducer |
| Data Analysis | Python / NumPy / SciPy (planned) |
| Visualization | Desktop Dashboard (planned) |

## Development Progress

| Development Area | Status |
|---|---|
| Research Concept | Defined |
| System Architecture | Documented |
| Hardware Integration | In Development |
| ESP32 Firmware | Planned |
| Multi-Sensor Acquisition | Planned |
| Signal Processing | Architecture Documented |
| Desktop Application | Planned |
| Experimental Validation | Pending |

<p align="center">
  <strong>CRANIAL MECHANICAL RESONANCE MAPPING</strong>
  <br>
  Experimental Biomedical Engineering Research Prototype
  <br><br>
  Engineering | Instrumentation | Signal Processing | Research
</p>
