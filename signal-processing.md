# Signal Processing and Mechanical Response Analysis

## Cranial Mechanical Resonance Mapping (CMRM)

**Document Type:** Signal Processing Technical Documentation
**Domain:** Digital Signal Processing and Experimental Biomechanics
**Input:** Multi-Sensor Acceleration Measurements
**Processing Platform:** Embedded Acquisition and Desktop Software
**Development Stage:** Experimental Research

---

# 1. Introduction

The signal-processing subsystem of Cranial Mechanical Resonance Mapping (CMRM) is designed to transform raw acceleration measurements into meaningful mechanical vibration characteristics.

The system acquires three-axis acceleration signals from multiple ADXL345 accelerometers positioned at different locations of a cranial headform.

The acquired signals are processed to investigate:

* Vibration amplitude.
* Frequency-domain characteristics.
* Candidate resonance frequencies.
* Frequency-dependent mechanical response.
* Spatial vibration distribution.
* Mechanical symmetry.
* Damping behaviour.
* Measurement repeatability.

The processing pipeline combines digital filtering, statistical analysis, frequency-domain transformation and mechanical response characterization.

The final objective is to generate a structured Mechanical Resonance Profile (MRP).

**Important:** A detected spectral peak is not automatically a validated mechanical resonance. Reliable resonance characterization requires controlled excitation, reference measurements and appropriate experimental validation.

---

# 2. Signal Processing Architecture

The proposed signal-processing pipeline consists of the following stages:

```text
MULTI-SENSOR DATA ACQUISITION
            |
            v
      RAW ACCELERATION
            |
            v
      SIGNAL VALIDATION
            |
            v
      BASELINE CORRECTION
            |
            v
       NOISE FILTERING
            |
            v
    TIMESTAMP ALIGNMENT
            |
            v
      FFT PROCESSING
            |
            v
    FREQUENCY SPECTRUM
            |
            v
      FEATURE EXTRACTION
            |
            v
 MECHANICAL RESPONSE ANALYSIS
            |
            v
  MECHANICAL RESONANCE PROFILE
            |
            v
      VISUALIZATION
            |
            v
    EXPERIMENTAL REPORT
```

---

# 3. Input Signal Acquisition

## 3.1 Sensor Configuration

The proposed CMRM architecture uses four ADXL345 accelerometers.

| Sensor ID | Position       | Measurement             |
| --------- | -------------- | ----------------------- |
| S1        | Left Temporal  | Three-axis acceleration |
| S2        | Right Temporal | Three-axis acceleration |
| S3        | Frontal        | Three-axis acceleration |
| S4        | Occipital      | Three-axis acceleration |

Each sensor provides three acceleration components:

$$
a_x(t),\quad a_y(t),\quad a_z(t)
$$

Where:

* \(a_x(t)\): X-axis acceleration.
* \(a_y(t)\): Y-axis acceleration.
* \(a_z(t)\): Z-axis acceleration.

Measurements should be converted into consistent physical units before further processing.

## 3.2 Resultant Acceleration

The resultant acceleration combines the three measured axes.

$$
a_r(t)=\sqrt{a_x(t)^2+a_y(t)^2+a_z(t)^2}
$$

This provides a representation of the total acceleration magnitude.

However, the resultant magnitude is nonlinear and can contain a DC component due to gravity and sensor orientation.

For vibration analysis, appropriate baseline correction or orientation-aware gravity compensation may be necessary.

## 3.3 Sampling Frequency

The sampling frequency determines how frequently the acceleration signal is recorded.

$$
f_s=\frac{1}{T_s}
$$

Where:

* \(f_s\): Sampling frequency in Hz.
* \(T_s\): Sampling interval in seconds.

For reliable digital signal processing, the sampling frequency must be selected according to the highest frequency of interest.

The Nyquist criterion states:

$$
f_s>2f_{max}
$$

Where \(f_{max}\) is the highest frequency component intended to be analyzed.

In practical systems, additional margin and appropriate anti-aliasing considerations are necessary.

The actual sampling frequency must be measured rather than assumed from the firmware configuration.

---

# 4. Signal Validation

Before processing, incoming sensor data must be evaluated for quality.

The validation stage checks:

* Missing samples.
* Invalid sensor readings.
* Communication failures.
* Unexpected discontinuities.
* Timestamp inconsistencies.
* Saturation.
* Abnormal measurement ranges.

Each measurement should include:

| Field          | Description           |
| -------------- | --------------------- |
| Sensor ID      | Sensor identification |
| Timestamp      | Acquisition time      |
| X acceleration | X-axis measurement    |
| Y acceleration | Y-axis measurement    |
| Z acceleration | Z-axis measurement    |
| Quality status | Measurement validity  |

Invalid or incomplete measurements should be flagged rather than silently replaced.

---

# 5. Baseline Correction

Baseline correction removes unwanted offsets from the acquired signal.

For a signal containing \(N\) samples, the mean value is:

$$
\bar{x}=\frac{1}{N}\sum_{n=0}^{N-1}x[n]
$$

The baseline-corrected signal is:

$$
x_c[n]=x[n]-\bar{x}
$$

Where:

* \(x[n]\): Original signal.
* \(\bar{x}\): Mean signal value.
* \(x_c[n]\): Corrected signal.

Baseline correction helps reduce constant offsets.

However, removing the mean does not necessarily remove gravity-related effects caused by changing sensor orientation.

For more advanced processing, orientation estimation and gravity compensation may be required.

---

# 6. Digital Noise Filtering

Sensor measurements may contain unwanted noise from:

* Electrical interference.
* Mechanical disturbances.
* Sensor noise.
* Power-supply fluctuations.
* Environmental vibrations.
* Communication-related measurement errors.

Filtering is used to improve the quality of the acquired signals.

## 6.1 Low-Pass Filtering

A low-pass filter allows lower-frequency components to pass while attenuating higher-frequency components.

Potential applications:

* Reducing high-frequency noise.
* Smoothing acceleration measurements.
* Removing unwanted high-frequency disturbances.

## 6.2 High-Pass Filtering

A high-pass filter attenuates low-frequency components.

Potential applications:

* Reducing baseline drift.
* Removing slow-varying components.
* Isolating vibration-related signal components.

## 6.3 Band-Pass Filtering

A band-pass filter retains a selected frequency range.

It can be useful when the experiment focuses on a predefined frequency interval.

## 6.4 Filter Design Considerations

Filter selection should consider:

* Sampling frequency.
* Cutoff frequency.
* Filter order.
* Passband characteristics.
* Stopband attenuation.
* Phase response.
* Group delay.

Zero-phase filtering may be used for suitable offline analyses, but it is non-causal and should not be confused with real-time filtering.

All filter parameters should be documented for reproducibility.

---

# 7. Time-Domain Analysis

Time-domain analysis investigates how acceleration changes over time.

It provides important information about the magnitude and behaviour of the measured vibration signal.

## 7.1 Root Mean Square (RMS) Acceleration

RMS represents the effective magnitude of a signal over a defined measurement interval.

$$
a_{RMS}=
\sqrt{\frac{1}{N}\sum_{n=0}^{N-1}a[n]^2}
$$

Where:

* \(a[n]\): Acceleration sample.
* \(N\): Number of samples.

RMS acceleration can be calculated separately for each sensor and measurement axis.

It can also be calculated using the resultant acceleration signal.

## 7.2 Peak Acceleration

Peak acceleration represents the maximum absolute acceleration magnitude.

$$
a_{peak}=\max |a[n]|
$$

## 7.3 Peak-to-Peak Acceleration

Peak-to-peak acceleration represents the difference between the maximum and minimum signal values.

$$
a_{p-p}=a_{max}-a_{min}
$$

These parameters help characterize vibration magnitude.

---

# 8. Frequency-Domain Analysis

Frequency-domain analysis investigates the frequency components present in an acceleration signal.

The primary transformation used in CMRM is the Fast Fourier Transform (FFT).

## 8.1 Fourier Transform

The Fourier Transform converts a time-domain signal into its frequency-domain representation.

For a continuous signal:

$$
X(f)=\int_{-\infty}^{\infty}x(t)e^{-j2\pi ft}dt
$$

This representation describes the frequency components contained in the signal.

## 8.2 Discrete Fourier Transform

For a discrete signal containing N samples:

$$
X[k]=\sum_{n=0}^{N-1}x[n]e^{-j2\pi kn/N}
$$

Where:

* \(x[n]\): Input signal.
* \(N\): Number of samples.
* \(k\): Frequency-bin index.
* \(X[k]\): Complex frequency-domain coefficient.

The FFT is an efficient computational method for calculating the DFT.

## 8.3 Frequency Resolution

Frequency resolution determines the spacing between adjacent frequency bins.

$$
\Delta f=\frac{f_s}{N}
$$

Where:

* \(f_s\): Sampling frequency.
* \(N\): Number of samples.

A larger measurement window generally provides finer frequency resolution.

However, measurement duration and signal stationarity must also be considered.

## 8.4 FFT Magnitude Spectrum

The magnitude spectrum is calculated as:

$$
M[k]=|X[k]|
$$

The resulting spectrum can be used to identify dominant frequency components.

For accurate amplitude interpretation, the FFT normalization, window correction and one-sided spectrum scaling must be implemented consistently.

---

# 9. Spectral Peak Detection

Spectral peak detection identifies local maxima in the frequency-domain magnitude spectrum.

Potential applications include:

* Identifying dominant frequency components.
* Comparing spectral characteristics.
* Tracking frequency changes.
* Detecting candidate resonance regions.

A candidate peak should satisfy defined criteria such as:

* Minimum prominence.
* Minimum amplitude.
* Frequency-range constraints.
* Minimum separation from neighbouring peaks.

The output should include:

| Parameter       | Description                                  |
| --------------- | -------------------------------------------- |
| Peak frequency  | Frequency corresponding to the detected peak |
| Peak magnitude  | Spectral magnitude                           |
| Peak prominence | Relative peak prominence                     |
| Peak index      | FFT bin position                             |
| Sensor ID       | Originating sensor                           |

**Important:** Spectral peak detection identifies frequency components. It does not independently establish mechanical resonance.

---

# 10. Frequency Response Analysis

Frequency-response analysis investigates the relationship between an excitation input and the resulting mechanical response.

For a controlled excitation experiment:

$$
H(f)=\frac{Y(f)}{U(f)}
$$

Where:

* \(U(f)\): Fourier representation of the excitation input.
* \(Y(f)\): Fourier representation of the measured response.
* \(H(f)\): Frequency-response function.

The magnitude is:

$$
|H(f)|=\frac{|Y(f)|}{|U(f)|}
$$

The phase is:

$$
\phi(f)=\angle H(f)
$$

A practical experimental system may estimate frequency response using cross-spectral and input auto-spectral density methods.

For example:

$$
H_1(f)=\frac{G_{yu}(f)}{G_{uu}(f)}
$$

Where:

* \(G_{yu}(f)\): Cross-spectral density between output and input.
* \(G_{uu}(f)\): Input auto-spectral density.

This approach requires an appropriately measured excitation reference signal.

If the excitation input is unknown or unmeasured, the system should report spectral characteristics rather than claiming a validated frequency-response function.

---

# 11. Mechanical Symmetry Analysis

Mechanical symmetry analysis compares responses from corresponding sensor locations.

The primary comparison in the proposed architecture is between:

* S1: Left Temporal.
* S2: Right Temporal.

A possible project-defined symmetry index is:

$$
MSI=1-\frac{|A_L-A_R|}{A_L+A_R+\epsilon}
$$

Where:

* \(A_L\): Left-side response amplitude.
* \(A_R\): Right-side response amplitude.
* \(\epsilon\): Small numerical stabilization constant.

The index is dependent on the chosen amplitude definition and should be evaluated only when the measurement conditions are comparable.

Its interpretation requires:

* Consistent sensor mounting.
* Comparable excitation conditions.
* Appropriate calibration.
* Valid measurement quality.
* Documented sensor orientation.

This index is an experimental comparison metric, not a clinical symmetry measure.

---

# 12. Damping Behaviour Analysis

Damping describes how vibration amplitude decreases over time after excitation is removed.

A suitable experimental approach is to measure the free-decay response.

For an underdamped single-degree-of-freedom system, the logarithmic decrement can be estimated using successive peaks.

$$
\delta=\frac{1}{n}\ln\left(\frac{x_0}{x_n}\right)
$$

Where:

* \(x_0\): Initial peak amplitude.
* \(x_n\): Peak amplitude after n cycles.
* \(n\): Number of cycles.

The damping ratio is:

$$
\zeta=\frac{\delta}{\sqrt{4\pi^2+\delta^2}}
$$

This relationship assumes an appropriate underdamped second-order model.

### Important Limitations

Reliable damping estimation requires:

* A suitable free-decay interval.
* Adequate signal-to-noise ratio.
* Identifiable successive peaks.
* Appropriate measurement duration.
* A valid mechanical model.

If the decay does not satisfy the assumptions, the damping ratio should be reported as unavailable or insufficient data.

---

# 13. Measurement Repeatability

Repeatability describes the consistency of measurements obtained under comparable experimental conditions.

For repeated measurements:

$$
\bar{x}=\frac{1}{N}\sum_{i=1}^{N}x_i
$$

The standard deviation is:

$$
s=\sqrt{\frac{\sum_{i=1}^{N}(x_i-\bar{x})^2}{N-1}}
$$

The coefficient of variation is:

$$
CV=\frac{s}{\bar{x}}\times100
$$

CV should be used carefully when the mean is close to zero.

Repeatability evaluation requires multiple measurements using the same:

* Sensor configuration.
* Mounting arrangement.
* Excitation conditions.
* Acquisition settings.
* Processing methodology.

---

# 14. Mechanical Resonance Profile (MRP)

The Mechanical Resonance Profile is the proposed consolidated output of the signal-processing subsystem.

It organizes the extracted mechanical response characteristics into a structured experimental representation.

## 14.1 MRP Parameters

| Parameter           | Description                                 |
| ------------------- | ------------------------------------------- |
| Resonance Frequency | Experimentally supported resonance estimate |
| Vibration Amplitude | Acceleration response magnitude             |
| Frequency Response  | Response across excitation frequencies      |
| Mechanical Symmetry | Comparative response between locations      |
| Damping Behaviour   | Characterization of vibration decay         |
| Repeatability       | Consistency across repeated experiments     |
| Spatial Response    | Differences between sensor locations        |

## 14.2 MRP Generation Workflow

```text
RAW SENSOR DATA
       |
       v
SIGNAL VALIDATION
       |
       v
PREPROCESSING
       |
       v
TIME-DOMAIN ANALYSIS
       |
       v
FFT ANALYSIS
       |
       v
FREQUENCY FEATURES
       |
       v
MECHANICAL RESPONSE ANALYSIS
       |
       v
PARAMETER VALIDATION
       |
       v
MECHANICAL RESONANCE PROFILE
```

Each parameter should include its measurement interval, units, calculation method and quality status.

Unavailable parameters should remain explicitly unavailable rather than being assigned fabricated values.

---

# 15. AI-Assisted Analysis

The proposed AI analysis engine explores computational methods for identifying patterns in mechanical vibration measurements.

Potential analytical modules include:

### Pattern Recognition

Identifies recurring characteristics in processed vibration signals.

### Resonance Candidate Detection

Identifies candidate spectral peaks and frequency regions.

### Frequency Response Analysis

Supports comparative interpretation of measured frequency-dependent responses.

### Mechanical Symmetry Analysis

Compares corresponding sensor measurements.

### Damping Behaviour Analysis

Investigates patterns in measured free-decay signals.

### Historical Comparison

Compares experimental results across different measurement sessions.

### Feature Classification

Explores classification of extracted signal features using validated datasets.

AI models should only be trained and evaluated using appropriately documented experimental data.

Synthetic demonstration data must be clearly distinguished from actual measurements.

AI outputs must not be presented as medical diagnoses.

---

# 16. Signal Quality Assessment

Measurement quality is essential for reliable analysis.

The processing pipeline should evaluate:

* Missing samples.
* Sensor saturation.
* Sampling irregularities.
* Signal discontinuities.
* Excessive noise.
* Communication errors.
* Invalid timestamps.
* Inadequate measurement duration.

Each measurement should receive an appropriate quality status.

Suggested statuses:

| Status            | Meaning                                        |
| ----------------- | ---------------------------------------------- |
| GOOD              | Measurement satisfies defined quality criteria |
| WARNING           | Measurement contains limitations               |
| INVALID           | Measurement cannot be reliably processed       |
| INSUFFICIENT DATA | Available samples are inadequate               |
| NOT AVAILABLE     | Parameter cannot currently be calculated       |

Quality thresholds must be experimentally established and documented.

---

# 17. Data Storage and Processing Output

Each processed experiment should preserve both raw and derived information.

Recommended output fields:

```text
experiment_id
session_id
sensor_id
timestamp
sampling_frequency
acceleration_x
acceleration_y
acceleration_z
resultant_acceleration
rms_acceleration
peak_acceleration
peak_to_peak
candidate_peak_frequency
spectral_magnitude
frequency_response
damping_ratio
symmetry_index
measurement_quality
processing_version
```

Not every field will be available for every experiment.

The software should record unavailable values explicitly.

Raw measurements should never be overwritten by processed measurements.

---

# 18. Software Implementation Recommendations

The signal-processing subsystem can be developed using Python.

Potential libraries:

| Library      | Purpose                               |
| ------------ | ------------------------------------- |
| NumPy        | Numerical computation                 |
| SciPy        | Digital filtering and signal analysis |
| Pandas       | Structured data processing            |
| Matplotlib   | Signal visualization                  |
| Plotly       | Interactive charts                    |
| Scikit-learn | Machine learning                      |
| SQLite       | Experimental metadata storage         |

Actual dependencies should be added to the project only when they are used by the implementation.

---

# 19. Validation and Testing

The signal-processing algorithms must be validated independently.

### Test 1: Synthetic Signal Validation

Generate signals with known frequencies and amplitudes.

Verify whether the FFT implementation identifies the expected frequency components.

### Test 2: Filtering Validation

Apply known noise components and verify filtering behaviour.

### Test 3: RMS Validation

Compare the implementation against manually calculated reference values.

### Test 4: Frequency Resolution

Verify FFT frequency-bin calculations using known sampling parameters.

### Test 5: Sensor Comparison

Compare measurements obtained from calibrated sensors under controlled mechanical conditions.

### Test 6: Frequency Response Validation

Use a known excitation reference and independently characterized mechanical test setup.

### Test 7: Damping Validation

Test logarithmic decrement calculations using known simulated free-decay signals before applying them to experimental measurements.

### Test 8: Repeatability

Perform repeated measurements under controlled conditions and evaluate variability.

---

# 20. Limitations

The current signal-processing architecture has several limitations.

1. Sequential multiplexer acquisition introduces timing differences.
2. Sensor orientation affects acceleration measurements.
3. Mechanical coupling influences measured amplitude.
4. FFT spectral peaks do not independently establish resonance.
5. Frequency-response estimation requires a measured excitation reference.
6. Damping estimation requires an appropriate free-decay response.
7. Mechanical symmetry indices depend on measurement consistency.
8. AI analysis requires validated experimental datasets.
9. Measurement uncertainty must be characterized.
10. Algorithmic outputs require independent verification.

---

# 21. Future Development

Potential future improvements include:

* Improved sensor synchronization.
* Advanced digital filtering.
* Windowed spectral analysis.
* Welch power spectral density estimation.
* Cross-spectral analysis.
* Frequency-response estimation.
* Automated spectral peak detection.
* Improved damping estimation.
* Spatial vibration visualization.
* Statistical repeatability analysis.
* Explainable machine-learning models.
* Automated experimental reporting.

---

# 22. Research and Safety Disclaimer

The signal-processing methods described in this document are intended for experimental mechanical vibration characterization.

CMRM is not a clinically validated diagnostic platform.

All initial experimental testing should use suitable non-human headforms or mechanical phantoms.

Analytical parameters must be interpreted within their mathematical assumptions and experimental limitations.

No clinical conclusions should be derived from unvalidated measurements or AI-generated outputs.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Document:** Signal Processing and Mechanical Response Analysis
**Category:** Digital Signal Processing and Experimental Biomechanics
**Status:** Research Methodology Documentation
