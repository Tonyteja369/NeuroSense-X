# CMRM Research Framework

## Cranial Mechanical Resonance Mapping

### Experimental Mechanical Vibration Characterization Using Multi-Sensor Embedded Systems

---

## 1. Research Overview

Cranial Mechanical Resonance Mapping (CMRM) is an experimental biomedical engineering research project focused on characterizing mechanical vibration responses across multiple locations of an experimental cranial headform.

The proposed system integrates embedded electronics, multi-channel acceleration sensing, controlled mechanical excitation, digital signal processing, and computational analysis.

Four ADXL345 accelerometers are proposed to acquire three-axis acceleration measurements from different locations on a mechanical headform.

The acquired measurements are processed to investigate frequency-dependent mechanical responses and generate a structured **Mechanical Resonance Profile (MRP)**.

The central research objective is to investigate whether a distributed multi-sensor measurement architecture can provide reproducible spatial and frequency-domain characterization of mechanical vibration in a controlled experimental model.

CMRM is an experimental research prototype, not a clinically validated medical device.

## 2. Research Motivation

Mechanical systems respond differently to external excitation depending on their physical properties, geometry, material characteristics, boundary conditions, and damping.

A mechanical headform can therefore be investigated through controlled excitation and vibration measurements.

Traditional single-point measurements provide information about the response at one measurement location. A multi-sensor configuration offers the opportunity to compare responses across multiple locations.

CMRM explores this approach using an embedded sensing platform.

The research is motivated by the following questions:

* Can an affordable embedded platform acquire useful mechanical vibration measurements?
* Can multiple accelerometers characterize spatial variations in mechanical response?
* Can frequency-domain analysis identify repeatable spectral characteristics?
* Can controlled excitation and measured input-output relationships support frequency-response estimation?
* Can repeated measurements establish the reliability of the proposed system?
* Can a structured MRP represent the measured mechanical characteristics?

These questions form the foundation of the experimental investigation.

## 3. Research Problem Statement

There is a need to investigate accessible, reproducible, and multi-location experimental platforms for characterizing mechanical vibration responses in cranial-shaped mechanical models.

The proposed CMRM platform addresses this research problem by integrating controlled excitation, distributed acceleration sensing, embedded acquisition, and computational signal analysis into a unified experimental framework.

The research focuses on the feasibility, measurement quality, repeatability, and characterization capabilities of the proposed system.

## 4. Research Aim

To design, develop, and experimentally evaluate an embedded multi-sensor platform for characterizing frequency-dependent mechanical vibration responses across multiple locations of an experimental cranial headform.

## 5. Research Objectives

The primary objectives are:

1. Design an embedded measurement architecture using an ESP32 controller.
2. Integrate four ADXL345 three-axis accelerometers.
3. Implement sensor communication through a TCA9548A I²C multiplexer.
4. Develop a controlled mechanical excitation subsystem.
5. Acquire acceleration measurements from multiple locations.
6. Develop a signal-processing pipeline for vibration analysis.
7. Investigate time-domain and frequency-domain characteristics.
8. Evaluate frequency-response estimation using an appropriate excitation reference.
9. Compare mechanical responses across sensor locations.
10. Generate a structured Mechanical Resonance Profile.
11. Evaluate measurement repeatability and system limitations.
12. Establish a foundation for future computational and AI-assisted analysis.

## 6. Proposed Research Questions

The investigation is organized around the following research questions.

| Research Question | Investigation                                                                    |
| ----------------- | -------------------------------------------------------------------------------- |
| RQ1               | Can the embedded platform acquire usable multi-sensor acceleration measurements? |
| RQ2               | Can the system identify repeatable frequency-domain characteristics?             |
| RQ3               | Do measured responses differ across sensor locations?                            |
| RQ4               | Can a measured excitation reference support frequency-response estimation?       |
| RQ5               | How repeatable are the measurements under controlled experimental conditions?    |
| RQ6               | Can the resulting features be organized into a reproducible MRP?                 |

These questions should be evaluated through experiments rather than assumed to be true.

## 7. Proposed Research Hypotheses

The following are preliminary, testable hypotheses.

**H1:** The proposed embedded system can acquire acceleration measurements from multiple sensor locations under controlled experimental conditions.

**H2:** Repeated measurements under equivalent experimental conditions can produce sufficiently consistent frequency-domain characteristics.

**H3:** The measured mechanical response may vary across sensor locations.

**H4:** A validated input-output measurement configuration can support frequency-response characterization of the experimental headform.

**H5:** A structured MRP can represent selected mechanical response characteristics derived from validated measurements.

These hypotheses must be evaluated using defined experimental methods and statistical analysis.

## 8. System Architecture

The proposed CMRM research platform consists of five major subsystems.

### 8.1 Embedded Controller

**Component:** ESP32 development board.

Responsibilities:

* Sensor communication.
* Acquisition control.
* Excitation control.
* Timestamp generation.
* Serial communication.
* System-state management.

The exact ESP32 variant and acquisition configuration must be documented before final experiments.

### 8.2 Multi-Sensor Measurement System

**Components:**

* Four ADXL345 accelerometers.
* TCA9548A I²C multiplexer.

Proposed sensor arrangement:

| Sensor | Experimental Location |
| ------ | --------------------- |
| S1     | Left temporal         |
| S2     | Right temporal        |
| S3     | Frontal               |
| S4     | Occipital             |

The sensor positions, orientation, mounting method, and calibration should remain consistent between repeated experiments.

### 8.3 Mechanical Excitation System

Proposed components:

* ESP32 signal generation.
* PAM8403 audio amplifier.
* Bone-conduction transducer.

The excitation subsystem should be characterized independently.

The actual excitation waveform, frequency response, output level, and measurement reference must be established experimentally.

A commanded PWM frequency should not automatically be treated as the actual mechanical input frequency or amplitude.

### 8.4 Power Management

Proposed components:

* Rechargeable Li-ion battery.
* TP4056 charging module.
* Appropriate voltage regulation.
* Power distribution.

The complete power architecture must be electrically verified, including charging protection, voltage compatibility, current requirements, and grounding.

### 8.5 Computational Analysis

The software and signal-processing environment performs:

* Data validation.
* Signal preprocessing.
* FFT analysis.
* Frequency-response estimation.
* Multi-sensor comparison.
* Feature extraction.
* MRP generation.
* Experimental reporting.

## 9. Research Methodology

The research follows an experimental development and validation methodology.

### Phase 1 — Literature Review

Review relevant research on:

* Mechanical vibration analysis.
* Experimental modal analysis.
* Accelerometer-based measurement.
* Frequency-response functions.
* Signal-processing techniques.
* Multi-sensor acquisition.
* Mechanical phantom characterization.

The literature review should identify existing methods, measurement challenges, and the specific research gap addressed by CMRM.

### Phase 2 — Hardware Development

Develop and integrate the embedded measurement platform.

Activities:

1. Configure the ESP32.
2. Integrate the TCA9548A.
3. Connect the four ADXL345 sensors.
4. Verify sensor communication.
5. Develop the excitation subsystem.
6. Implement power management.
7. Test serial communication.

### Phase 3 — Sensor Characterization

Characterize the sensors before conducting mechanical experiments.

Investigations should include:

* Sensor offsets.
* Noise characteristics.
* Axis orientation.
* Scale-factor accuracy.
* Frequency response.
* Output data rate.
* Saturation limits.
* Mounting consistency.

Sensor specifications should be distinguished from experimentally measured performance.

### Phase 4 — Experimental Setup

Use a controlled non-human cranial mechanical headform or suitable mechanical phantom.

Document:

* Phantom geometry.
* Material characteristics, where known.
* Mechanical boundary conditions.
* Sensor positions.
* Sensor orientation.
* Mounting method.
* Excitation location.
* Excitation configuration.
* Environmental conditions.

The experimental setup should be photographed and documented to support reproducibility.

### Phase 5 — Data Acquisition

Acquire measurements under controlled experimental conditions.

Recommended procedure:

1. Verify hardware readiness.
2. Confirm sensor availability.
3. Record baseline measurements.
4. Configure the excitation.
5. Record the excitation reference.
6. Start acquisition.
7. Record sensor measurements.
8. Repeat the experiment.
9. Save raw measurements and metadata.

The sequential channel selection of the TCA9548A must be considered when evaluating multi-sensor timing.

### Phase 6 — Signal Processing

Process the acquired measurements using the CMRM signal-processing engine.

Operations include:

* Data validation.
* Timestamp verification.
* Baseline correction.
* Digital filtering.
* Time-domain analysis.
* FFT analysis.
* Spectral peak detection.
* Frequency-response estimation.
* Multi-sensor comparison.
* Feature extraction.

All processing parameters should be documented.

### Phase 7 — Mechanical Response Characterization

Investigate:

* Dominant spectral components.
* Frequency-dependent response.
* Response amplitude.
* Phase characteristics.
* Spatial response differences.
* Measurement repeatability.
* Damping estimates, where appropriate data are available.

The identification of mechanical resonance requires more than detecting a spectral peak. Experimental excitation and system-response characteristics must support the interpretation.

### Phase 8 — MRP Generation

Combine validated measurements and extracted features into a structured Mechanical Resonance Profile.

The MRP should include:

* Experiment identification.
* Sensor-wise measurements.
* Frequency characteristics.
* Response amplitudes.
* Spatial comparisons.
* Signal-quality information.
* Repeatability information.
* Processing configuration.

### Phase 9 — Experimental Validation

Evaluate whether the system produces reliable and reproducible results.

Compare repeated measurements and assess uncertainty.

Document failed experiments and measurement limitations rather than excluding them without explanation.

## 10. Experimental Design

The experimental design should control the variables that influence mechanical response.

### 10.1 Independent Variables

Potential independent variables include:

* Excitation frequency.
* Excitation amplitude.
* Excitation duration.
* Excitation location.
* Mechanical boundary conditions.
* Phantom configuration.

### 10.2 Dependent Variables

Potential dependent variables include:

* Acceleration amplitude.
* RMS acceleration.
* Dominant spectral frequency.
* Frequency-response magnitude.
* Frequency-response phase.
* Spatial response distribution.
* Repeatability measurements.

### 10.3 Controlled Variables

Variables to maintain or document include:

* Phantom configuration.
* Sensor mounting.
* Sensor orientation.
* Sensor calibration.
* Excitation position.
* Sampling configuration.
* Environmental conditions.
* Measurement duration.

## 11. Experimental Validation Metrics

The system should be evaluated using objective metrics.

| Validation Area          | Potential Metric                                        |
| ------------------------ | ------------------------------------------------------- |
| Sensor Accuracy          | Comparison with a suitable reference instrument         |
| Sensor Noise             | Noise floor and standard deviation                      |
| Sampling Stability       | Sampling interval variation                             |
| Frequency Identification | Error against known reference frequencies               |
| Frequency Response       | Agreement with an independently characterized reference |
| Repeatability            | Coefficient of variation                                |
| Multi-Sensor Consistency | Channel-to-channel comparison                           |
| Data Integrity           | Missing and invalid sample rate                         |
| System Reliability       | Successful acquisition rate                             |

### 11.1 Coefficient of Variation

For repeated measurements:

$$
CV=\frac{\sigma}{\mu}\times100
$$

Where:

* \(\sigma\) = Standard deviation.
* \(\mu\) = Mean measurement value.

The coefficient of variation is useful when the mean is sufficiently far from zero and the measured quantity is appropriate for ratio-based comparison.

### 11.2 Frequency Estimation Error

$$
E_f=|f_{measured}-f_{reference}|
$$

Relative frequency error can be expressed as:

$$
E_{relative}=
\frac{|f_{measured}-f_{reference}|}
{f_{reference}}\times100
$$

The reference frequency must be independently known or measured.

### 11.3 Repeatability

Repeated experiments should use equivalent experimental configurations.

The analysis should report:

* Number of trials.
* Mean.
* Standard deviation.
* Coefficient of variation.
* Measurement uncertainty.
* Any excluded measurements and the reasons for exclusion.

## 12. Statistical Analysis

Statistical analysis should be selected according to the experimental design.

Potential methods include:

* Descriptive statistics.
* Standard deviation.
* Confidence intervals.
* Repeatability analysis.
* Correlation analysis.
* Paired comparisons.
* Analysis of variance, where assumptions are satisfied.

Statistical significance alone does not establish physical or clinical significance.

The study should report effect sizes and uncertainty where appropriate.

## 13. Mechanical Resonance Profile

The MRP is the primary structured analytical output proposed by the CMRM platform.

It aims to summarize experimentally measured mechanical response characteristics across multiple sensor locations.

Proposed MRP structure:

| Category           | Parameters                                     |
| ------------------ | ---------------------------------------------- |
| Identification     | Experiment ID, phantom ID, date                |
| Acquisition        | Sampling rate, sensor configuration            |
| Sensor Response    | RMS, peak, peak-to-peak                        |
| Spectral Analysis  | Dominant frequencies, spectral characteristics |
| Frequency Response | Magnitude, phase, coherence                    |
| Spatial Analysis   | Sensor-wise response comparison                |
| Symmetry           | Left-right comparison                          |
| Damping            | Validated damping estimates                    |
| Repeatability      | Variation across trials                        |
| Quality            | Signal-quality and validation status           |

The MRP is an experimental characterization framework. It is not a medical report.

## 14. AI-Assisted Research Opportunities

AI integration may be investigated after the acquisition and signal-processing pipelines are validated.

Potential research directions:

### 14.1 Signal Quality Classification

Investigate machine-learning methods for identifying:

* Noisy measurements.
* Sensor disconnection.
* Saturation.
* Unstable acquisition.
* Abnormal experimental signal quality.

### 14.2 Spectral Pattern Analysis

Investigate whether computational models can identify recurring spectral patterns across repeated mechanical experiments.

### 14.3 Anomaly Detection

Explore methods for identifying measurements that differ substantially from established experimental baselines.

### 14.4 Multi-Sensor Feature Analysis

Investigate relationships between features extracted from different sensor locations.

### 14.5 Explainable Analysis

Explore interpretable methods that show which measured features contribute to a model-generated result.

AI outputs must remain separate from directly measured physical quantities.

## 15. Research Reproducibility

Reproducibility is essential for credible experimental research.

Every experiment should preserve:

* Hardware configuration.
* Firmware version.
* Software version.
* Sensor calibration information.
* Sensor placement.
* Excitation settings.
* Sampling configuration.
* Raw measurements.
* Processing parameters.
* Extracted features.
* Experimental observations.

A researcher should be able to reproduce the analysis using the original raw dataset and documented processing configuration.

## 16. Research Limitations

The current CMRM architecture has several important limitations.

### Hardware Limitations

* ADXL345 bandwidth and noise characteristics may constrain measurements.
* Sensor mounting can influence measured vibration.
* Multiplexer-based acquisition introduces inter-channel timing differences.
* Excitation waveform distortion may affect experimental results.
* Power-supply noise may influence measurements.

### Experimental Limitations

* Mechanical phantom properties may not represent biological tissue.
* Boundary conditions can change measured responses.
* Environmental vibration can introduce unwanted signals.
* Excitation repeatability must be established.
* Sensor positioning errors can affect spatial comparisons.

### Computational Limitations

* FFT peaks do not independently establish mechanical resonance.
* Frequency-response estimates require a suitable input reference.
* Damping estimates require appropriate measurement conditions.
* AI-generated interpretations require independent evaluation.

### Clinical Limitations

CMRM has no established clinical validity or diagnostic capability.

Measurements from a mechanical phantom cannot automatically be generalized to human cranial biomechanics or medical conditions.

## 17. Ethical and Safety Considerations

Initial research should use a non-human mechanical headform or suitable mechanical phantom.

Any future investigation involving human participants requires:

* Appropriate institutional ethics approval.
* Informed consent.
* Defined research protocols.
* Electrical and mechanical safety assessment.
* Appropriate supervision.
* Data privacy protections.

The system must not be used for diagnosis or medical decision-making.

## 18. Publication Roadmap

Potential research outputs include:

### Paper 1 — Embedded Hardware and Measurement Architecture

**Proposed title:**

"Design and Development of a Low-Cost Embedded Multi-Sensor Platform for Experimental Cranial Mechanical Vibration Characterization"

Focus:

* Embedded system architecture.
* Multi-sensor integration.
* Excitation subsystem.
* Acquisition methodology.
* Hardware characterization.

### Paper 2 — Signal Processing and Mechanical Response Analysis

**Proposed title:**

"Multi-Channel Signal Processing and Frequency-Response Characterization for Cranial Mechanical Resonance Mapping"

Focus:

* Signal acquisition.
* Preprocessing.
* FFT analysis.
* Frequency-response estimation.
* Spatial comparison.
* Repeatability.

### Paper 3 — Computational Mechanical Response Analysis

**Proposed title:**

"Computational Feature Extraction and Explainable Analysis of Multi-Sensor Mechanical Vibration Measurements"

Focus:

* Feature extraction.
* Multi-sensor analysis.
* Pattern recognition.
* Explainable computational methods.
* Experimental validation.

These are proposed publication directions, not claims that the corresponding research results have already been established.

## 19. Current Research Status

| Research Component             | Status                          |
| ------------------------------ | ------------------------------- |
| Research concept               | Proposed                        |
| System architecture            | Proposed                        |
| Hardware integration           | To be completed and verified    |
| Sensor characterization        | Pending experimental validation |
| Excitation characterization    | Pending experimental validation |
| Data acquisition               | To be implemented / verified    |
| Signal-processing pipeline     | Proposed                        |
| MRP framework                  | Proposed                        |
| Mechanical phantom experiments | Pending                         |
| Repeatability evaluation       | Pending                         |
| AI-assisted analysis           | Future development              |
| Research publication           | Not yet established             |

Update this table based on actual experimental progress.

## 20. Future Research Directions

Potential future investigations include:

* Improved sensor synchronization.
* Reference-instrument-based validation.
* Advanced experimental modal analysis.
* Improved excitation measurement.
* Uncertainty quantification.
* Mechanical phantom comparisons.
* Advanced frequency-response estimation.
* Computational mechanical modelling.
* Explainable AI for signal characterization.
* Reproducible open research datasets.

## 21. Conclusion

CMRM proposes an integrated experimental platform combining embedded electronics, multi-sensor acceleration measurements, controlled mechanical excitation, digital signal processing, and computational analysis.

The research will investigate whether this architecture can provide reproducible and meaningful mechanical vibration characterization across multiple locations of an experimental cranial headform.

The scientific contribution must ultimately be established through controlled experiments, reference measurements, uncertainty analysis, and reproducible validation.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Research Module:** Experimental Methodology and Validation
**Version:** 0.1.0
**Status:** Proposed Research Framework

**Disclaimer:** CMRM is an experimental biomedical engineering research prototype. It is not a clinically validated medical device or diagnostic system.
