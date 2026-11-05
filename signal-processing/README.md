# CMRM Signal Processing Engine

## 1. Overview

The CMRM Signal Processing Engine is responsible for transforming raw vibration measurements acquired from the ADXL345 accelerometers into structured signal characteristics for experimental mechanical-response analysis.

The system receives three-axis acceleration measurements from four proposed sensor locations:

* S1 — Left temporal
* S2 — Right temporal
* S3 — Frontal
* S4 — Occipital

The processing engine analyzes these measurements in both the time and frequency domains.

Its primary objective is to characterize how the experimental headform responds to controlled mechanical excitation and generate a structured **Mechanical Resonance Profile (MRP)**.

The processing engine is intended for experimental research. Its outputs are not clinical measurements or diagnostic conclusions.

## 2. Objectives

The signal-processing engine is designed to:

* Validate acquired acceleration data.
* Handle timestamps and sampling information.
* Identify missing or invalid measurements.
* Perform baseline correction.
* Apply appropriate digital filters.
* Analyze signals in the time domain.
* Perform Fast Fourier Transform (FFT) analysis.
* Identify dominant spectral components.
* Characterize frequency-dependent mechanical responses.
* Compare responses across sensor locations.
* Extract experimentally supported features.
* Generate structured MRP data.
* Preserve reproducibility through processing metadata.

## 3. Signal Processing Architecture

```text
             RAW ACCELEROMETER DATA
                       |
                       |
                DATA VALIDATION
                       |
                       |
              TIMESTAMP VERIFICATION
                       |
                       |
               BASELINE CORRECTION
                       |
                       |
                 DIGITAL FILTERING
                       |
                       |
                SIGNAL SEGMENTATION
                       |
                       |
              TIME-DOMAIN ANALYSIS
                       |
                       |
                  FFT ANALYSIS
                       |
                       |
             FREQUENCY-DOMAIN FEATURES
                       |
                       |
             FREQUENCY RESPONSE ANALYSIS
                       |
                       |
              MULTI-SENSOR COMPARISON
                       |
                       |
               FEATURE EXTRACTION
                       |
                       |
             MECHANICAL RESPONSE ANALYSIS
                       |
                       |
              MRP GENERATION
```

Each stage should produce identifiable outputs so that intermediate results can be inspected and validated.

## 4. Input Signal Structure

The primary input is three-axis acceleration data from each ADXL345 sensor.

A measurement can be represented as:

$$
a(t)=
\begin{bmatrix}
a_x(t)\\
a_y(t)\\
a_z(t)
\end{bmatrix}
$$

Where:

* \(a_x(t)\) = X-axis acceleration.
* \(a_y(t)\) = Y-axis acceleration.
* \(a_z(t)\) = Z-axis acceleration.

The acceleration unit should be recorded explicitly, such as m/s² or g.

Example input:

```json
{
  "sensor_id": "S1",
  "timestamp_ms": 12500,
  "ax": 0.12,
  "ay": 0.08,
  "az": 0.95,
  "unit": "m/s2"
}
```

This is an illustrative data packet, not a measured result.

## 5. Sampling Frequency

Sampling frequency determines how many measurements are acquired per second.

It is represented by:

$$
f_s=\frac{1}{T_s}
$$

Where:

* \(f_s\) = Sampling frequency in Hz.
* \(T_s\) = Sampling interval in seconds.

For example, if the sampling interval is 1 millisecond:

$$
f_s=\frac{1}{0.001}=1000\ Hz
$$

### 5.1 Nyquist Criterion

The Nyquist criterion establishes the minimum theoretical sampling frequency needed to represent a band-limited signal.

$$
f_s>2f_{max}
$$

Where:

* \(f_s\) = Sampling frequency.
* \(f_{max}\) = Highest frequency of interest.

In practical systems, additional margin is needed for anti-alias filtering, sensor bandwidth, sampling imperfections, and the characteristics of the signal being measured.

**Important:** The actual ADXL345 output data rate and the complete acquisition path must support the selected sampling configuration. A software setting alone cannot guarantee the requested sampling rate.

## 6. Data Validation

Before signal processing, acquired measurements must pass basic validation.

Checks include:

* Missing samples.
* Duplicate samples.
* Invalid numerical values.
* Sensor disconnection.
* Timestamp inconsistencies.
* Unexpected sampling intervals.
* Saturation or clipping.
* Excessive noise.
* Insufficient measurement duration.

Invalid measurements should be flagged rather than silently modified.

The processing engine should retain the original raw dataset.

## 7. Timestamp Alignment

Each sensor measurement should contain a timestamp.

This is especially important because the TCA9548A I²C multiplexer selects sensor channels sequentially.

Therefore, measurements from the four sensors may not be simultaneous.

For multi-sensor comparison, the processing engine should:

1. Retain the original timestamps.
2. Estimate the sampling interval of each channel.
3. Identify timing irregularities.
4. Quantify inter-channel acquisition delays.
5. Apply an appropriate alignment method when justified.
6. Record any interpolation or resampling performed.

Resampling should not create the impression that measurements were originally simultaneous.

## 8. Baseline Correction

Baseline correction removes unwanted offsets from the measured signal.

A simple mean-removal operation is:

$$
x_c(t)=x(t)-\bar{x}
$$

Where:

* \(x(t)\) = Original signal.
* \(\bar{x}\) = Mean signal value.
* \(x_c(t)\) = Corrected signal.

Python example:

```python
import numpy as np

def remove_mean(signal):
    signal = np.asarray(signal, dtype=float)
    return signal - np.mean(signal)
```

Mean removal can be useful for analyzing dynamic vibration components.

However, it should not be applied blindly. Gravity, mounting orientation, sensor offsets, and the physical meaning of the acceleration components must be considered.

## 9. Digital Filtering

Digital filtering helps isolate the frequency components relevant to the experiment.

Potential filter types include:

| Filter    | Purpose                                          |
| --------- | ------------------------------------------------ |
| Low-pass  | Reduces high-frequency components                |
| High-pass | Reduces low-frequency components                 |
| Band-pass | Retains a selected frequency band                |
| Notch     | Suppresses a narrow unwanted frequency component |

### 9.1 Butterworth Filter

A Butterworth filter provides a maximally flat passband response.

Python example:

```python
from scipy.signal import butter, sosfiltfilt

def apply_bandpass(signal, fs, low_cutoff, high_cutoff, order=4):
    if not 0 < low_cutoff < high_cutoff < fs / 2:
        raise ValueError("Invalid filter cutoff frequencies.")

    sos = butter(
        order,
        [low_cutoff, high_cutoff],
        btype="bandpass",
        fs=fs,
        output="sos"
    )

    return sosfiltfilt(sos, signal)
```

The example uses forward-backward filtering for offline analysis. It is not a zero-latency real-time filtering implementation.

Filter parameters must be documented and selected according to the experimental frequency range.

## 10. Time-Domain Analysis

Time-domain analysis describes how acceleration changes over time.

### 10.1 Mean

$$
\bar{x}=\frac{1}{N}\sum_{n=1}^{N}x[n]
$$

### 10.2 Root Mean Square (RMS)

RMS represents the effective magnitude of a signal.

$$
x_{RMS}=\sqrt{\frac{1}{N}\sum_{n=1}^{N}x[n]^2}
$$

### 10.3 Peak Amplitude

$$
x_{peak}=\max(|x[n]|)
$$

### 10.4 Peak-to-Peak Amplitude

$$
x_{pp}=x_{max}-x_{min}
$$

Python implementation:

```python
import numpy as np

def extract_time_features(signal):
    signal = np.asarray(signal, dtype=float)

    if signal.size == 0:
        raise ValueError("Signal cannot be empty.")

    if not np.all(np.isfinite(signal)):
        raise ValueError("Signal contains invalid values.")

    return {
        "mean": float(np.mean(signal)),
        "rms": float(np.sqrt(np.mean(signal ** 2))),
        "peak": float(np.max(np.abs(signal))),
        "peak_to_peak": float(np.ptp(signal))
    }
```

These features should be calculated separately for each sensor axis when axis-level analysis is required.

## 11. Frequency-Domain Analysis

Frequency-domain analysis identifies the frequency components present in the acquired signal.

The Fast Fourier Transform (FFT) is an efficient algorithm for calculating the Discrete Fourier Transform.

The DFT is:

$$
X[k]=\sum_{n=0}^{N-1}x[n]e^{-j2\pi kn/N}
$$

Where:

* \(x[n]\) = Input signal.
* \(X[k]\) = Frequency-domain representation.
* \(N\) = Number of samples.
* \(k\) = Frequency-bin index.

The frequency corresponding to a bin is:

$$
f_k=\frac{k f_s}{N}
$$

### 11.1 Frequency Resolution

The FFT frequency resolution is:

$$
\Delta f=\frac{f_s}{N}
$$

For example:

If:

$$
f_s=1000\ Hz
$$

and:

$$
N=1000
$$

Then:

$$
\Delta f=1\ Hz
$$

A longer acquisition duration generally improves frequency resolution when the sampling frequency remains constant.

### 11.2 Windowing

Finite signal segments can produce spectral leakage.

Window functions reduce discontinuities at the segment boundaries.

Common windows include:

* Hann window.
* Hamming window.
* Blackman window.
* Rectangular window.

The Hann window is a useful starting point for many vibration-analysis applications.

Python example:

```python
import numpy as np

def calculate_fft(signal, fs):
    signal = np.asarray(signal, dtype=float)

    if signal.size < 2:
        raise ValueError("At least two samples are required.")

    if not np.all(np.isfinite(signal)):
        raise ValueError("Signal contains invalid values.")

    signal = signal - np.mean(signal)

    window = np.hanning(len(signal))
    windowed_signal = signal * window

    spectrum = np.fft.rfft(windowed_signal)
    frequencies = np.fft.rfftfreq(len(signal), d=1/fs)

    amplitude = np.abs(spectrum)

    return frequencies, amplitude
```

This is a basic FFT demonstration. A production implementation should apply appropriate amplitude normalization, window correction, and spectral estimation conventions.

## 12. Dominant Frequency Detection

The dominant frequency is the frequency associated with the strongest selected spectral component.

A simple approach is:

$$
f_d=f_k \quad \text{where } |X[k]| \text{ is maximum}
$$

The DC component should generally be excluded when searching for vibration-related spectral peaks.

Python example:

```python
import numpy as np
from scipy.signal import find_peaks

def detect_spectral_peaks(frequencies, amplitude):
    frequencies = np.asarray(frequencies)
    amplitude = np.asarray(amplitude)

    if frequencies.size != amplitude.size:
        raise ValueError("Frequency and amplitude arrays must match.")

    if frequencies.size < 3:
        return []

    peaks, properties = find_peaks(amplitude)

    results = []

    for index in peaks:
        results.append({
            "frequency_hz": float(frequencies[index]),
            "amplitude": float(amplitude[index])
        })

    return sorted(
        results,
        key=lambda item: item["amplitude"],
        reverse=True
    )
```

Peak prominence, minimum separation, noise level, and the frequency range of interest should be considered when identifying meaningful peaks.

**Important:** A dominant spectral peak is not automatically a mechanical resonance frequency.

## 13. Frequency Response Function

Frequency-response analysis is central to mechanical system characterization.

A frequency response function describes the relationship between an input excitation and a measured output response.

For a linear system:

$$
H(f)=\frac{Y(f)}{U(f)}
$$

Where:

* \(U(f)\) = Measured input excitation spectrum.
* \(Y(f)\) = Measured output response spectrum.
* \(H(f)\) = Frequency response function.

A more robust experimental approach may use spectral estimation.

For example, the H1 estimator is:

$$
H_1(f)=\frac{G_{yu}(f)}{G_{uu}(f)}
$$

Where:

* \(G_{yu}(f)\) = Cross-spectral density between output and input.
* \(G_{uu}(f)\) = Input autospectral density.

The quality of this estimate depends on the excitation reference, measurement quality, system assumptions, and coherence.

### 13.1 Magnitude

$$
|H(f)|
$$

Represents the magnitude of the system response relative to the input.

### 13.2 Phase

$$
\angle H(f)
$$

Represents the phase difference between the input and output.

### 13.3 Coherence

Magnitude-squared coherence can be calculated as:

$$
\gamma^2(f)=
\frac{|G_{yu}(f)|^2}
{G_{uu}(f)G_{yy}(f)}
$$

Where:

* \(G_{yy}(f)\) = Output autospectral density.
* \(G_{uu}(f)\) = Input autospectral density.
* \(G_{yu}(f)\) = Cross-spectral density.

Coherence provides information about the consistency of the measured input-output relationship.

A valid excitation reference must be measured or otherwise established. The commanded excitation frequency alone is not necessarily a sufficient measurement of the physical input.

## 14. Mechanical Resonance Characterization

Mechanical resonance occurs when a system exhibits a strong dynamic response near one or more characteristic frequencies under appropriate excitation conditions.

The CMRM project aims to characterize frequency-dependent mechanical responses of an experimental headform.

A potential resonance candidate should be evaluated using:

* Frequency-response magnitude.
* Phase behavior.
* Repeatability.
* Excitation characteristics.
* Measurement quality.
* Sensor mounting consistency.
* Experimental boundary conditions.

The software should distinguish between:

1. Dominant spectral frequency.
2. Candidate resonance frequency.
3. Experimentally validated resonance characteristic.

These terms must not be used interchangeably.

## 15. Damping Estimation

Damping describes the reduction of vibration amplitude over time in a dissipative system.

For a suitable underdamped system with approximately exponential free decay, logarithmic decrement can be estimated using:

$$
\delta=\frac{1}{n}\ln\left(\frac{x_1}{x_{n+1}}\right)
$$

Where:

* \(x_1\) = Initial peak amplitude.
* \(x_{n+1}\) = Peak amplitude after \(n\) cycles.
* \(\delta\) = Logarithmic decrement.

The damping ratio is:

$$
\zeta=\frac{\delta}{\sqrt{4\pi^2+\delta^2}}
$$

This method requires appropriate free-decay data and assumptions about the system.

It should not be applied directly to arbitrary forced-vibration measurements.

## 16. Multi-Sensor Analysis

The CMRM platform uses four proposed sensor locations to compare spatial mechanical responses.

The software should calculate features independently for each sensor.

Possible comparisons include:

* RMS acceleration.
* Peak amplitude.
* Dominant spectral components.
* Frequency-response magnitude.
* Phase.
* Signal quality.
* Repeatability.

### 16.1 Left-Right Comparison

For equivalent left and right measurement locations, a response symmetry index may be calculated as:

$$
SI=
\frac{|A_L-A_R|}
{(A_L+A_R)/2}\times100
$$

Where:

* \(A_L\) = Left-side response.
* \(A_R\) = Right-side response.

This calculation requires comparable sensor calibration, axis orientation, acquisition timing, and excitation conditions.

It is an experimental comparison metric, not a measure of clinical symmetry.

## 17. Mechanical Resonance Profile (MRP)

The MRP is the structured output of the CMRM signal-processing pipeline.

Proposed components:

| MRP Parameter         | Description                                       |
| --------------------- | ------------------------------------------------- |
| Sensor Identification | Measurement location                              |
| Dominant Frequency    | Strongest identified spectral component           |
| RMS Acceleration      | Effective acceleration magnitude                  |
| Peak Acceleration     | Maximum absolute acceleration                     |
| Frequency Response    | Measured output relative to excitation            |
| Response Distribution | Spatial variation across sensors                  |
| Symmetry Index        | Comparison between corresponding sensor responses |
| Damping Estimate      | Model-based damping estimate, when valid          |
| Repeatability         | Consistency across repeated experiments           |
| Signal Quality        | Measurement validation result                     |

The MRP should include the processing configuration and experimental metadata needed to interpret the results.

## 18. Data Output Format

An example MRP data structure:

```json
{
  "experiment_id": "CMRM_EXP_001",
  "processing_version": "0.1.0",
  "analysis_status": "completed",
  "sensors": {
    "S1": {
      "rms_acceleration": 0.24,
      "peak_acceleration": 0.71,
      "dominant_frequency_hz": 125.0,
      "quality": "valid"
    },
    "S2": {
      "rms_acceleration": 0.22,
      "peak_acceleration": 0.68,
      "dominant_frequency_hz": 124.0,
      "quality": "valid"
    }
  },
  "notes": [
    "Illustrative data only",
    "Not a clinical measurement"
  ]
}
```

The values are examples only. Actual MRP output must be calculated from acquired and validated experimental measurements.

## 19. Proposed Software Structure

```text
signal-processing/
│
├── README.md
│
├── preprocessing/
│   ├── __init__.py
│   ├── validation.py
│   ├── baseline.py
│   ├── filtering.py
│   └── alignment.py
│
├── time_domain/
│   ├── __init__.py
│   └── features.py
│
├── frequency_domain/
│   ├── __init__.py
│   ├── fft_analysis.py
│   ├── peak_detection.py
│   └── spectral_analysis.py
│
├── frequency_response/
│   ├── __init__.py
│   ├── frf.py
│   └── coherence.py
│
├── mechanical_analysis/
│   ├── __init__.py
│   ├── resonance.py
│   ├── damping.py
│   └── spatial_comparison.py
│
├── feature_extraction/
│   ├── __init__.py
│   └── extract_features.py
│
├── mrp/
│   ├── __init__.py
│   └── generate_profile.py
│
└── tests/
    ├── test_validation.py
    ├── test_filtering.py
    ├── test_fft.py
    └── test_features.py
```

This is the proposed folder structure. Files should be added as their corresponding modules are implemented.

## 20. Testing and Validation

The signal-processing engine should be validated using known reference signals before processing experimental hardware measurements.

### Stage 1 — Synthetic Signal Testing

Generate signals with known frequencies and amplitudes.

Verify:

* FFT frequency identification.
* Frequency resolution.
* Amplitude estimation.
* Peak detection.

### Stage 2 — Filtering Validation

Test filters using synthetic signals containing:

* Desired frequency components.
* Out-of-band noise.
* Multiple frequency components.

Verify that the output behaves as expected.

### Stage 3 — Sensor Data Validation

Evaluate:

* Sensor noise.
* Offset.
* Axis consistency.
* Sampling intervals.
* Measurement saturation.
* Channel timing differences.

### Stage 4 — Mechanical Testing

Use a controlled mechanical phantom and repeatable excitation conditions.

Evaluate:

* Frequency-dependent response.
* Sensor-to-sensor consistency.
* Repeatability.
* Excitation reference quality.
* Measurement uncertainty.

### Stage 5 — Reproducibility

Run identical processing configurations on the same raw dataset and verify that the results are reproducible within defined numerical tolerances.

## 21. Research Limitations

The accuracy of mechanical-response characterization depends on the complete experimental system.

Important limitations include:

* ADXL345 bandwidth and noise characteristics.
* Sensor mounting and orientation.
* Sequential multiplexer acquisition.
* Timing uncertainty.
* Excitation waveform distortion.
* Mechanical boundary conditions.
* Environmental vibration.
* Signal-processing assumptions.

The processing engine cannot compensate automatically for poorly calibrated sensors or uncontrolled experimental conditions.

The system must not claim that measured mechanical response directly establishes a medical condition.

## 22. Future Development

Potential future improvements include:

* Advanced spectral estimation.
* Improved multi-channel synchronization.
* Automated resonance candidate identification.
* Model-based mechanical parameter estimation.
* Uncertainty quantification.
* Advanced repeatability analysis.
* Automated signal-quality classification.
* AI-assisted feature interpretation.
* More advanced spatial response visualization.

## 23. Disclaimer

The CMRM Signal Processing Engine is intended for experimental mechanical vibration characterization and biomedical engineering research.

Its outputs are not clinically validated and must not be interpreted as medical diagnoses or used to make clinical decisions.

---

**Project:** Cranial Mechanical Resonance Mapping (CMRM)
**Module:** Signal Processing Engine
**Version:** 0.1.0
**Status:** Proposed Technical Architecture
