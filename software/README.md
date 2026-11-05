# CMRM Software — Desktop Application and Data Intelligence Platform

## 1. Overview

The CMRM Software is the proposed desktop application for the Cranial Mechanical Resonance Mapping (CMRM) experimental platform.

It acts as the central interface between the hardware acquisition system and the researcher. The application is designed to receive measurements from the ESP32, monitor multiple accelerometer channels, manage experimental sessions, perform signal analysis, visualize mechanical responses, and generate structured experimental reports.

The software aims to transform raw acceleration measurements into an interpretable **Mechanical Resonance Profile (MRP)**.

The application is intended for experimental research and mechanical characterization using a non-human headform or suitable mechanical phantom. It is not a clinically validated diagnostic system.

## 2. Software Objectives

The primary objectives are:

* Establish communication with the ESP32 controller.
* Monitor the connection and operational status of the hardware.
* Receive measurements from four ADXL345 accelerometers.
* Display live acceleration signals.
* Manage experimental sessions and measurement configurations.
* Support controlled frequency-sweep experiments.
* Process acquired vibration signals.
* Perform frequency-domain analysis.
* Compare responses across sensor locations.
* Generate mechanical response visualizations.
* Calculate experimentally supported MRP parameters.
* Support AI-assisted experimental data interpretation.
* Export structured research reports and datasets.

## 3. Proposed Software Architecture

The application follows a modular architecture that separates the user interface, hardware communication, data management, signal processing, and analysis components.

```text
                 CMRM DESKTOP APPLICATION
                           |
             +-------------+-------------+
             |                           |
        USER INTERFACE              APPLICATION CORE
             |                           |
    +--------+---------+       +---------+----------+
    |        |         |       |         |          |
Dashboard  Live     Experiment  Device   Data     Configuration
           Monitor  Management  Manager  Manager  Manager
                                   |
                            SERIAL COMMUNICATION
                                   |
                              ESP32 FIRMWARE
                                   |
                         TCA9548A MULTIPLEXER
                                   |
                         FOUR ADXL345 SENSORS
                                   |
                         EXPERIMENTAL DATA
                                   |
                    +--------------+--------------+
                    |              |              |
                Validation    Signal Processing  Storage
                                   |
                         Frequency Analysis
                                   |
                         Response Characterization
                                   |
                         MECHANICAL RESONANCE
                             PROFILE (MRP)
                                   |
                         Visualization & Reports
```

This is a proposed logical architecture. The final implementation will depend on the selected programming language, desktop framework, and communication protocol.

## 4. Main Software Modules

### 4.1 Overview Dashboard

The dashboard provides a consolidated view of the experimental system.

Proposed dashboard elements:

* Hardware connection status.
* ESP32 controller status.
* Sensor availability.
* Active experiment information.
* Acquisition status.
* Latest measurement information.
* Signal-quality indicators.
* Processing status.
* Experimental warnings and errors.

The dashboard should clearly distinguish between normal operation, warnings, and critical system errors.

### 4.2 Device Connection Manager

This module manages communication between the desktop application and the ESP32.

Responsibilities:

* Detect available serial communication ports.
* Allow the user to select the ESP32 connection.
* Configure the baud rate.
* Establish and terminate serial communication.
* Receive firmware messages.
* Detect communication interruptions.
* Handle invalid or incomplete packets.
* Display connection errors.

The communication layer should not assume that a serial connection is always available.

### 4.3 Experiment Management

This module manages individual experimental sessions.

Each experiment should contain:

| Parameter                | Description                                    |
| ------------------------ | ---------------------------------------------- |
| Experiment ID            | Unique experiment identifier                   |
| Session Name             | Descriptive experiment name                    |
| Date and Time            | Experiment creation timestamp                  |
| Headform ID              | Identification of the mechanical test object   |
| Sensor Configuration     | Number and location of sensors                 |
| Sampling Frequency       | Configured acquisition rate                    |
| Excitation Frequency     | Applied excitation frequency or sweep range    |
| Excitation Configuration | Experimental excitation settings               |
| Operator ID              | Researcher or operator identifier, if required |
| Experiment Notes         | Additional observations                        |

The software should support:

* Creating a new experiment.
* Loading an existing experiment.
* Starting and stopping acquisition.
* Saving experimental metadata.
* Adding observations.
* Reviewing previous measurements.

### 4.4 Live Sensor Monitoring

The live monitoring module displays acceleration measurements received from the four sensor channels.

Proposed sensor arrangement:

| Sensor | Position       | Channel |
| ------ | -------------- | ------- |
| S1     | Left temporal  | CH0     |
| S2     | Right temporal | CH1     |
| S3     | Frontal        | CH2     |
| S4     | Occipital      | CH3     |

These locations represent the proposed experimental configuration and must be confirmed during physical sensor installation.

Each sensor should have an independent monitoring panel.

Display parameters:

* X-axis acceleration.
* Y-axis acceleration.
* Z-axis acceleration.
* Acceleration magnitude.
* Timestamp.
* Sensor communication status.
* Signal-quality status.

Acceleration magnitude can be calculated as:

$$
a_{mag}=\sqrt{a_x^2+a_y^2+a_z^2}
$$

The application should preserve the original axis measurements rather than storing only the magnitude.

**Important:** The TCA9548A selects I²C channels sequentially. Therefore, the application must not assume that all four sensors were sampled simultaneously. Measurement timestamps and acquisition delays must be considered during comparison.

### 4.5 Signal Visualization Module

The visualization module provides graphical representations of the acquired signals.

Proposed visualizations:

**A. Time-Domain Signal**

Displays acceleration against time.

$$
x(t)=\text{Acceleration measured over time}
$$

Useful for observing:

* Transient vibrations.
* Signal stability.
* Mechanical disturbances.
* Changes in vibration amplitude.

**B. Frequency-Domain Spectrum**

Displays signal amplitude against frequency.

The Fast Fourier Transform (FFT) is used to convert the time-domain signal into its frequency-domain representation.

$$
X(f)=FFT\{x(t)\}
$$

Useful for identifying:

* Dominant frequency components.
* Spectral peaks.
* Harmonic components.
* Frequency-dependent vibration characteristics.

**C. Multi-Sensor Comparison**

Displays measurements from S1, S2, S3, and S4 together.

This supports comparison of the mechanical response across different measurement locations.

**D. Frequency Response Visualization**

When a valid excitation reference is available, the software can calculate and display the frequency response.

A basic frequency-response relationship is:

$$
H(f)=\frac{Y(f)}{U(f)}
$$

Where:

* \(H(f)\) = Frequency response function.
* \(Y(f)\) = Measured output response.
* \(U(f)\) = Measured excitation input.

A frequency-response function should not be calculated by simply dividing an output spectrum by an assumed excitation frequency. The input reference and estimation method must be properly defined.

### 4.6 Frequency Sweep Controller

This module supports controlled frequency-sweep experiments.

Proposed workflow:

1. Configure the starting frequency.
2. Configure the ending frequency.
3. Define the frequency increment.
4. Configure the excitation duration.
5. Configure the measurement duration.
6. Verify hardware readiness.
7. Start the experiment.
8. Record sensor measurements.
9. Associate measurements with the corresponding excitation settings.
10. Complete the sweep and save the experiment.

Example configuration:

| Parameter            |   Example |
| -------------------- | --------: |
| Start Frequency      |     20 Hz |
| End Frequency        |    500 Hz |
| Frequency Step       |      5 Hz |
| Excitation Duration  | 2 seconds |
| Measurement Duration | 3 seconds |

These are illustrative software configuration values, not validated experimental settings. Actual limits must be established through hardware characterization and safety testing.

### 4.7 Signal Processing Engine

This module performs signal-processing operations on acquired measurements.

Proposed processing sequence:

```text
Raw Sensor Data
      |
Data Validation
      |
Timestamp Verification
      |
Baseline Correction
      |
Filtering
      |
Signal Segmentation
      |
FFT Analysis
      |
Feature Extraction
      |
Frequency Response Analysis
      |
Mechanical Response Characterization
      |
MRP Generation
```

Potential extracted parameters:

| Parameter              | Description                                              |
| ---------------------- | -------------------------------------------------------- |
| Mean Acceleration      | Average acceleration                                     |
| RMS Acceleration       | Effective signal magnitude                               |
| Peak Acceleration      | Maximum measured value                                   |
| Peak-to-Peak Amplitude | Difference between maximum and minimum                   |
| Dominant Frequency     | Frequency of the strongest identified spectral component |
| Spectral Energy        | Energy represented within a defined frequency range      |
| Frequency Response     | Output response relative to measured excitation          |
| Damping Estimate       | Estimated damping under an appropriate validated model   |

The application must distinguish measured values, calculated features, and model-derived estimates.

### 4.8 Sensor Comparison and Spatial Mapping

This module compares mechanical responses across the four sensor locations.

Proposed functions:

* Compare acceleration amplitudes.
* Compare dominant frequency components.
* Display sensor-wise frequency spectra.
* Calculate left-right response differences.
* Visualize spatial response distributions.
* Identify measurement inconsistencies.

For example, a left-right comparison can be performed using:

$$
SI=\frac{|A_L-A_R|}{(A_L+A_R)/2}\times100
$$

Where:

* \(A_L\) = Left-side response amplitude.
* \(A_R\) = Right-side response amplitude.
* \(SI\) = Symmetry index.

This metric is meaningful only when the signals are appropriately calibrated, aligned, and measured under comparable conditions.

Spatial mapping should be treated as an experimental visualization, not an anatomical or clinical interpretation.

### 4.9 Mechanical Resonance Profile Generator

The MRP Generator is the main analytical component of the CMRM software.

It combines experimentally supported measurements and calculated features into a structured profile.

Proposed MRP components:

1. Sensor-wise response characteristics.
2. Dominant spectral frequencies.
3. Frequency-response characteristics.
4. Vibration amplitude distribution.
5. Spatial response comparison.
6. Symmetry measurements.
7. Damping estimates, when valid.
8. Experimental repeatability.
9. Signal-quality indicators.

The software should not automatically label every spectral peak as a mechanical resonance.

A resonance interpretation requires appropriate experimental evidence, including excitation characteristics, measurement conditions, and repeatability.

### 4.10 AI-Assisted Analysis Module

An optional AI-assisted module may be integrated after the fundamental signal-processing pipeline has been validated.

Potential applications:

* Identify unusual signal patterns.
* Classify experimental signal conditions.
* Detect potential acquisition anomalies.
* Compare experimental sessions.
* Generate descriptive summaries.
* Assist researchers in interpreting extracted features.

The AI module should receive validated features and relevant metadata rather than blindly processing unverified measurements.

Every AI-generated interpretation should be labelled as model-generated and should not be presented as a confirmed physical finding.

AI analysis must not be presented as medical diagnosis.

### 4.11 Report Generation Module

The reporting module converts experimental results into structured research documentation.

Proposed report sections:

1. Experiment identification.
2. Experimental objective.
3. Hardware configuration.
4. Sensor arrangement.
5. Acquisition parameters.
6. Raw signal summary.
7. Signal-quality assessment.
8. Time-domain analysis.
9. Frequency-domain analysis.
10. Sensor comparison.
11. Mechanical response characteristics.
12. MRP summary.
13. Limitations.
14. Experimental observations.

Proposed export formats:

* PDF
* CSV
* JSON
* PNG

The report should retain the distinction between raw measurements, processed measurements, calculated features, and AI-generated observations.

### 4.12 Calibration Module

The calibration module supports sensor calibration and experimental configuration.

Proposed functions:

* Sensor identification.
* Axis orientation configuration.
* Offset calibration.
* Scale-factor verification.
* Sensor consistency checks.
* Calibration record management.
* Calibration date tracking.

Calibration values should be stored with the experiment so that results can be reproduced.

### 4.13 Settings and System Configuration

This module manages:

* Serial port selection.
* Baud rate.
* Sampling configuration.
* Sensor mapping.
* Filtering parameters.
* Frequency analysis settings.
* Data storage location.
* Report export settings.
* Application logging.

Critical experimental parameters should be recorded in the experiment metadata.

## 5. Hardware Communication Protocol

The desktop application requires a defined communication protocol to exchange information with the ESP32.

A JSON-based serial protocol is proposed.

Example sensor data packet:

```json
{
  "message_type": "sensor_data",
  "experiment_id": "CMRM_EXP_001",
  "timestamp_ms": 12500,
  "sensor_id": "S1",
  "channel": 0,
  "acceleration": {
    "x": 0.12,
    "y": 0.08,
    "z": 0.95
  },
  "unit": "m/s2",
  "status": "valid"
}
```

Example device status packet:

```json
{
  "message_type": "device_status",
  "device": "ESP32",
  "connection": "connected",
  "sensor_count": 4,
  "system_state": "READY"
}
```

These packets are proposed examples. They are not evidence that the current firmware already implements this exact protocol.

The final protocol should define:

* Message boundaries.
* Message types.
* Timestamp conventions.
* Units.
* Sequence numbers.
* Error messages.
* Invalid-data representation.
* Protocol version.
* Acknowledgements for control commands.

The software should reject malformed packets and log communication errors.

## 6. Proposed Data Model

The application should maintain separate records for experiments, sensor measurements, processed features, and reports.

### 6.1 Experiment Record

```json
{
  "experiment_id": "CMRM_EXP_001",
  "experiment_name": "Headform Frequency Response Test",
  "created_at": "2026-11-05T10:00:00",
  "headform_id": "HF_001",
  "sampling_rate_hz": 1000,
  "sensor_count": 4,
  "status": "completed"
}
```

### 6.2 Sensor Measurement Record

```json
{
  "experiment_id": "CMRM_EXP_001",
  "sensor_id": "S1",
  "timestamp_ms": 12500,
  "ax": 0.12,
  "ay": 0.08,
  "az": 0.95,
  "unit": "m/s2",
  "quality": "valid"
}
```

### 6.3 Processed Feature Record

```json
{
  "experiment_id": "CMRM_EXP_001",
  "sensor_id": "S1",
  "rms_acceleration": 0.24,
  "peak_acceleration": 0.71,
  "dominant_frequency_hz": 125.0,
  "processing_status": "completed"
}
```

The values above are illustrative and must not be interpreted as actual experimental measurements.

## 7. Data Storage Strategy

The software should preserve the original acquired measurements.

Recommended logical separation:

```text
CMRM_DATA/
│
├── experiments/
│   └── CMRM_EXP_001/
│       │
│       ├── metadata.json
│       ├── raw_data/
│       │   ├── S1.csv
│       │   ├── S2.csv
│       │   ├── S3.csv
│       │   └── S4.csv
│       │
│       ├── processed_data/
│       │   ├── filtered_signals.csv
│       │   ├── fft_results.csv
│       │   └── extracted_features.json
│       │
│       ├── visualizations/
│       │   ├── time_domain.png
│       │   ├── frequency_spectrum.png
│       │   └── sensor_comparison.png
│       │
│       └── reports/
│           └── experiment_report.pdf
```

Raw data should never be silently overwritten by processed data.

Every processing operation should record its configuration, including filter settings, sampling frequency, window function, FFT parameters, and software version.

## 8. Signal Quality and Validation

The software should assess signal quality before producing an MRP.

Potential validation checks:

* Missing measurements.
* Invalid numerical values.
* Sensor disconnection.
* Unexpected timestamps.
* Duplicate packets.
* Communication interruptions.
* Saturation or clipping.
* Excessive noise.
* Insufficient signal duration.
* Inconsistent sampling intervals.

Suggested quality states:

| Status  | Meaning                                      |
| ------- | -------------------------------------------- |
| VALID   | Measurement passed defined quality checks    |
| WARNING | Measurement is available but has limitations |
| INVALID | Measurement failed essential validation      |
| MISSING | Expected measurement was not received        |

The thresholds for these states must be defined through experimental characterization rather than arbitrary assumptions.

## 9. Error Handling

The application should handle errors without silently losing experimental data.

| Error                          | Recommended Response                                   |
| ------------------------------ | ------------------------------------------------------ |
| ESP32 disconnected             | Stop new acquisition and display warning               |
| Invalid serial packet          | Reject packet and record error                         |
| Sensor unavailable             | Mark the affected sensor as unavailable                |
| Missing timestamp              | Flag the measurement for validation                    |
| Storage failure                | Display critical warning and preserve recoverable data |
| Processing failure             | Retain raw data and record processing error            |
| Invalid configuration          | Prevent experiment start                               |
| Unexpected application closure | Recover the last saved experiment where possible       |

The application should maintain an event log for debugging and reproducibility.

## 10. Proposed Technology Stack

The final stack has not yet been confirmed.

Possible implementation choices include:

| Component            | Possible Technology    |
| -------------------- | ---------------------- |
| Programming Language | Python                 |
| Desktop UI           | PySide6 / PyQt         |
| Serial Communication | PySerial               |
| Numerical Processing | NumPy                  |
| Signal Processing    | SciPy                  |
| Data Visualization   | Matplotlib / PyQtGraph |
| Data Storage         | SQLite / CSV / JSON    |
| Report Generation    | ReportLab              |
| AI Integration       | PyTorch / Scikit-learn |
| Testing              | Pytest                 |

These are recommendations, not confirmed project dependencies. The implementation should prioritize reliability, maintainability, and compatibility with the target computer.

## 11. Installation and Execution

Installation instructions will be finalized after the software stack and project dependencies are selected.

The eventual installation guide should cover:

1. Installing the required programming environment.
2. Downloading or cloning the CMRM repository.
3. Installing dependencies.
4. Connecting the ESP32.
5. Identifying the serial port.
6. Configuring sensor communication.
7. Starting the desktop application.
8. Performing a hardware connection test.
9. Running a test acquisition.

Do not publish unverified installation commands as official instructions.

## 12. Testing Strategy

The software should be tested independently before being integrated with the complete hardware system.

### Stage 1 — Interface Testing

* Verify dashboard rendering.
* Verify navigation.
* Verify configuration handling.
* Verify experiment creation.

### Stage 2 — Communication Testing

* Test serial-port detection.
* Test ESP32 connection.
* Test valid JSON packets.
* Test malformed packets.
* Test communication interruptions.

### Stage 3 — Data Testing

* Test sensor data parsing.
* Verify timestamp handling.
* Verify unit consistency.
* Verify raw-data storage.
* Verify missing-data detection.

### Stage 4 — Signal Processing Testing

* Test filtering.
* Test FFT calculations.
* Test frequency resolution.
* Test feature extraction.
* Compare results with independently generated reference signals.

### Stage 5 — Integration Testing

* Connect the ESP32.
* Verify all sensor channels.
* Perform controlled mechanical excitation.
* Compare measurements across repeated experiments.
* Verify report generation.

Synthetic test signals should be clearly labelled as synthetic and must not be mixed with actual experimental measurements without identification.

## 13. Security and Data Integrity

The software should follow basic data-security principles.

* Do not hardcode API keys or credentials.
* Do not expose private experimental datasets.
* Validate incoming serial data.
* Avoid executing commands received from untrusted data.
* Preserve original measurements.
* Maintain experiment identifiers.
* Record processing parameters.
* Handle application crashes safely.
* Restrict access to sensitive research data where applicable.

## 14. Current Development Status

The following table distinguishes the intended functionality from verified implementation.

| Component                 | Development Status           |
| ------------------------- | ---------------------------- |
| Software architecture     | Proposed                     |
| Desktop dashboard         | To be implemented / verified |
| ESP32 communication       | To be implemented / verified |
| Live sensor monitoring    | To be implemented / verified |
| Experiment management     | To be implemented / verified |
| Signal visualization      | To be implemented / verified |
| FFT analysis              | To be implemented / verified |
| Frequency sweep interface | To be implemented / verified |
| MRP generation            | To be implemented / verified |
| AI-assisted analysis      | Future development           |
| Report generation         | To be implemented / verified |

Update this table as individual modules are actually developed and tested.

## 15. Limitations

The software is an experimental research interface.

It does not establish clinical validity, anatomical accuracy, or diagnostic capability.

The reliability of its results depends on:

* Sensor calibration.
* Mechanical mounting.
* Excitation repeatability.
* Sampling configuration.
* Timestamp accuracy.
* Signal-processing methodology.
* Experimental validation.

A dominant frequency in an acceleration spectrum should not automatically be interpreted as a mechanical resonance.

Any future human-subject investigation requires appropriate ethical approval, informed consent, and safety review.

## 16. Future Development

Potential future improvements include:

* Automated hardware detection.
* Advanced multi-sensor synchronization.
* Improved frequency-response estimation.
* Interactive spatial response visualization.
* Automated experiment comparison.
* Reproducible processing pipelines.
* AI-assisted signal-quality assessment.
* Advanced report customization.
* Experiment database management.
* Hardware-in-the-loop testing.

## 17. Contribution Guidelines

Contributors should:

1. Follow the modular software architecture.
2. Keep hardware communication separate from signal processing.
3. Document new modules.
4. Include tests for new processing functions.
5. Preserve raw measurements.
6. Avoid unsupported clinical claims.
7. Document dependencies and configuration changes.
8. Submit reproducible examples where possible.

## 18. Project Disclaimer

CMRM is an experimental biomedical engineering research prototype intended for mechanical vibration characterization and signal-processing research.

It is not a certified medical device, diagnostic instrument, or clinically validated system. Experimental results must not be used for medical decisions.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Repository:** CMRM
**Module:** Desktop Software and Data Intelligence
**Status:** Proposed Software Architecture
